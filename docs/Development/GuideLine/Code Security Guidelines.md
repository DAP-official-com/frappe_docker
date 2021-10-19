# Code Security Guidelines

## Prefer query builder over raw SQL
    If you are writing simple SQL queries there is a high chance it can be achieved using the Frappe Query Builder.

Don't use **frappe.db.sql** for simple queries like this:

```python
result = frappe.db.sql('select name, title, description from tabToDo where owner = "john@example.com"')
```

Use **frappe.db.get_all** instead:

```python
result = frappe.db.get_all('ToDo', fields=['name', 'title', 'description'], filters={'owner': 'john@example.com'})
```

> Read the full API [documentation](https://frappeframework.com/docs/user/en/api/document).

## Make your raw SQLs injection proof

    If there are scenarios that you have to write raw SQL queries, make sure to account for SQL injections via user input that may be passed in a web request.

Don't use *.format* to substitute variables.

```python
result = frappe.db.sql('select first_name from tabUser where name="{}"'.format(user))
# Pass variables to the sql method as a second parameter and they will be automatically sanitized and quoted.

result = frappe.db.sql('select first_name from tabUser where name=%s', [user])
#You can also use named variables and pass them as a dictionary:

accounting_periods = frappe.db.sql("""
	SELECT
		ap.name as name
	FROM
		`tabAccounting Period` ap,
		`tabClosed Document` cd
	WHERE
		ap.name = cd.parent
		AND ap.company = %(company)s
		AND cd.closed = 1
		AND cd.document_type = %(voucher_type)s
		AND %(date)s between ap.start_date and ap.end_date
	""", {
		'date': posting_date,
		'company': company,
		'voucher_type': voucher_type
	})

# If for some reason, you have to use .format to build your queries, make sure to sanitize your variables using frappe.db.escape.

result = frappe.db.sql('select first_name from tabUser where name={}'.format(frappe.db.escape(user)))
# Don't allow creation of arbitrary documents via web request
# Let's say you have created an API method create_document:

```

## api.py

```python
@frappe.whitelist()
def create_document(values):
  doc = frappe.get_doc(values).insert(ignore_permissions=True)
  return doc
```

This looks like a simple helper at first, but it allows a user to create any document on the site, since it bypasses the permissions check. Make sure to add additional checks if you really want arbitrary document creation.

You can use a combination of `frappe.only_for` method to restrict the method to System Managers and some manual checks. For e.g.,

```pyhon
@frappe.whitelist()
def create_document(values):
  frappe.only_for('System User')
  
  if values['doctype'] not in ('ToDo', 'Note', 'Task'):
    frappe.throw('Invalid Document Type')

  doc = frappe.get_doc(values).insert(ignore_permissions=True)
  return doc
```

[Code Security Guidelines REF:](https://github.com/frappe/erpnext/wiki/Code-Security-Guidelines)