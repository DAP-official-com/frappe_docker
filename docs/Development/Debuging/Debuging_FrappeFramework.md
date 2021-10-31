# Debug Frappe

This document lays down guidelines and best practices to debug performance issues in ERPNext.
This is document is a wiki, so please keep adding as you discover more interesting approaches :)

## Memory and CPU utilization

```shell
# to check process and resource usage on machine
htop
```

> Note:First place to check is Memory and CPU. htop is a great utility for that

- Check CPU load factor
- Check memory utilization / SWAP memory

If memory is full or CPU load factor is more than the number of cores, then we can assume bottlenecks are in the database. If these are okay and even then the system seems sweating, then the bottleneck may be elsewhere

Here is what a good htop should look like:

## ngxtop

An easy way to monitor the **Nginx** access log is the python lib ngxtop. You can install ngxtop with pip

```shell
pip install ngxtop
ngxtop
```

## MariaDB settings

- innodb_buffer_pool_size
     The biggest impact is of innodb_buffer_pool_size
     Check the value by running select variables like "innodb_buffer%"

>Note: Default 128M is very low. Expert opinion is that it should be 70-80% of total memory

To check mariaDB buffer size go to Check mysql settings at `etc/my.cnf` or `etc/mysql/my.cnf`

## Slow Query Log

Enable slow query log to start logging queries that take a long time

## Profiling

An easy way of identifying bottlenecks is profiling. You can profile a slow transaction by trying to execute that transaction in the frappe console. You can use the frappe API to quickly copy an existing doc and editing it

> Hint: %prun [statement] will automatically profile in ipython.

Here is a quick example of debugging a slow Stock Entry.

```
frappe@XXXXX:~/frappe-bench$ bench --site xxxxxxxx console

Python 2.7.12 (default, Nov 19 2016, 06:48:10)
Type "copyright", "credits" or "license" for more information.

IPython 5.5.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: frappe.get_all('Stock Entry', limit=1)
Out[1]: [{u'name': u'STE-17764'}]

In [2]: st = frappe.get_doc('Stock Entry', 'STE-17764')

In [3]: st1 = frappe.copy_doc(st)

In [4]: %prun st1.insert()
         332278 function calls (326875 primitive calls) in 1.028 seconds

   Ordered by: internal time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
       44    0.242    0.006    0.288    0.007 {cPickle.loads}
      173    0.208    0.001    0.208    0.001 {method 'recv_into' of '_socket.socket' objects}
    14639    0.034    0.000    0.045    0.000 copy_reg.py:46(_reconstructor)
     9822    0.018    0.000    0.067    0.000 connections.py:345(read_length_coded_string)
     9532    0.014    0.000    0.018    0.000 connections.py:240(read)
    22419    0.013    0.000    0.014    0.000 {isinstance}
    10002    0.013    0.000    0.018    0.000 connections.py:289(read_uint8)
     3884    0.013    0.000    0.247    0.000 connections.py:1017(_read_bytes)
    10002    0.013    0.000    0.031    0.000 connections.py:327(read_length_encoded_integer)
     1206    0.013    0.000    0.087    0.000 connections.py:410(_parse_field_descriptor)
     7138    0.012    0.000    0.030    0.000 {method 'decode' of 'str' objects}
     1942    0.012    0.000    0.356    0.000 connections.py:981(_read_packet)
    14647    0.011    0.000    0.011    0.000 {built-in method __new__ of type object at 0x906bc0}
4154/4152    0.011    0.000    0.056    0.000 base_document.py:95(get)
     6138    0.010    0.000    0.017    0.000 utf_8.py:15(decode)
22021/22004    0.010    0.000    0.010    0.000 {len}
     4415    0.009    0.000    0.026    0.000 socket.py:227(meth)
10773/10761    0.009    0.000    0.012    0.000 {getattr}
      160    0.008    0.000    0.147    0.001 connections.py:1474(_get_descriptions)
    14474    0.008    0.000    0.008    0.000 {method 'get' of 'dict' objects}
       83    0.008    0.000    0.035    0.000 base_document.py:789(_filter)
      246    0.008    0.000    0.055    0.000 connections.py:1456(_read_row_from_packet)
     4054    0.007    0.000    0.007    0.000 {method 'settimeout' of '_socket.socket' objects}
       17    0.007    0.000    0.009    0.001 sre_compile.py:256(_optimize_charset)
     3578    0.007    0.000    0.012    0.000 data.py:734(compare)
      140    0.007    0.000    0.007    0.000 {method 'recv' of '_socket.socket' objects}
     3578    0.007    0.000    0.019    0.000 __init__.py:1022(compare)
```

If the process does not seem to complete, after a few seconds, hit `Ctrl+C`. 
The function where this breaks, will give you a clue what is the bottleneck method.

## Mariadb SHOW PROCESSLIST

When a process feels "stuck", to understand what goes on in the database, enter the mariadb console and run `SHOW PROCESSLIST`

> Tip: Keep your python and mysql consoles on separate terminal tabs

```
$ bench --site xxxxxx mariadb
> show processlist;
***************************[ 1. row ]***************************
Id       | 4695068
User     | 1110823008c2a27b
Host     | 172.31.29.167:38884
db       | 1110823008c2a27b
Command  | Query
Time     | 2
State    | Copying to tmp table
Info     | select batch_id, sum(actual_qty) as qty from `tabBatch` join `tabStock Ledger Entry` on `tabBatch`.b
```

This shows the query that is slowing down the operation.

## Indexing

Once you identify a bottleneck, you can do a full code search / grep in the repository to find where the query is.

Then you can go to the mariadb console and run `EXPLAIN` command to see what is holding the query up

Example:

```
xxxxxx> EXPLAIN select batch_id, sum(actual_qty) as qty from `tabBatch` join `tabStock Ledger Entry`
              -> on `tabBatch`.batch_id = `tabStock Ledger Entry`.batch_no where `tabStock Ledger Entry`.item_
              -> code = "YYYY" and  `tabStock Ledger Entry`.warehouse = "XXXXX" and (`tabBatch`.expiry_d
              -> ate >= CURDATE() or `tabBatch`.expiry_date IS NULL) group by batch_id order by `tabBatch`.exp
              -> iry_date ASC, `tabBatch`.creation ASC;
***************************[ 1. row ]***************************
id            | 1
select_type   | SIMPLE
table         | tabStock Ledger Entry
type          | ref
possible_keys | warehouse,item_code,batch_index,batch_item_warehouse
key           | warehouse
key_len       | 563
ref           | const
rows          | 1
Extra         | Using index condition; Using where; Using temporary; Using filesort
***************************[ 2. row ]***************************
id            | 1
select_type   | SIMPLE
table         | tabBatch
type          | ALL
possible_keys | <null>
key           | <null>
key_len       | <null>
ref           | <null>
rows          | 2148
Extra         | Using where; Using join buffer (flat, BNL join)
Once you figure out the query, it should be interesting to fix this by adding an index

alter table `tabStock Ledger Entry` add index batch_item_warehouse (batch_no, item_code, warehouse);
```

You can then run the query again and see the impact!

## Gunicorn Workers

ERPNext uses Gunicorn HTTP server in production mode.

For optimal performance the number of Gunicorn workers needs to be set according to the number of CPU cores your serve has.

Recommended number is 2 * num_cores + 1.

It is specified in common_site_config.json file in frappe-bench/sites folder.

If you have installed ERPNext using easy install script, then the script will have set the appropriate number of workers already.

If you have installed ERPNext manually, then you need to set it. Default value is 1.

Also if you resize your VPS, you need to adjust the number of workers accordingly.
