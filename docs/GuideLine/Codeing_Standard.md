Coding Standards
Yefri Tavarez Nolasco edited this page on Jul 20, 2018 · 3 revisions
User facing messages must be translated
All user facing strings / text must be wrapped in the __("") function in javascript and _("") function in Python, so that it is shown as translated to the user.

Avoid using Deprecated API
The API used in the pull request must be the latest recommended methods and usage of globals like cur_frm must be avoided.

Examples:

$c_obj()
cur_frm
get_query
add_fetch
Break functions / methods more than 10 lines of code
Long functions are hard to read and debug, and 10 lines is usually a good point to break the function into smaller parts. Smaller functions are easy to read and review. You might even want to convert a series of functions into a class so you don't need to pass parameters through all of them.

Use simple indenting
The ERPNext and Frappe Project uses tabs (I know and we are sorry, but its too much effort to change it now and we don't want to lose the history). The indentation must be consistent whether you are writing Javascript or Python. Multi-line strings or expressions must also be consistently indented, not hanging like a bee hive at the end of the line. We just think the code looks a lot more stable that way.

Order your JavaScript handlers
Place above standard form handlers like onload, refresh, validate, etc. Then your handlers like validate_tax_amount, fetch_customers_from_server, etc. go in the middle of the script. And finally the form field's handlers. For example, if you're in the Sales Invoice and want to add handler for the customer field, that should go the bottom.

frappe.ui.form.on("Sales Invoice", {
  "setup": function(frm) {
    // todo
  },
  "onload": function(frm) {
    // todo
  },
  "validate": function(frm) {
    // todo
  },
  "validate_tax_amount": function(frm) {
    // todo
  },
  "fetch_customers_from_server": function(frm) {
    // todo
  },
  "customer": function(frm) {
    // todo
  }
});
This is wrong:

frappe.db.sql("""select item_name, description, default_warehouse from tabItem 
                                    where disabled = 0""")              
This is better:

frappe.db.sql("""SELECT 
        item_name, description, default_warehouse 
    FROM
        tabItem 
    WHERE
        disabled = 0""")              
Make your SQLs injection proof
If you are writing direct SQL query, don't use .format to replace strings. Example frappe.db.sql('select age from tabUser where name='{}'.format(user)) is wrong. It should be frappe.db.sql('select age from tabUser where name=%s', user)

Use simple structures
Code must be easy to read, so don't try clubbing multiple conditions in one line and make it complex. If you have a line that has multiple AND or OR, break it up into multiple conditions.

Function sequence
If you have multiple functions in a file, the calling function should be on the top and the called functions should be below. Check the example below:

def fa():
  fb()
  fc()

def fb():
  pass

def fc():
  pass


[REF](https://github.com/YefriTavarez/fimax/wiki/Coding-Standards#user-facing-messages-must-be-translated)