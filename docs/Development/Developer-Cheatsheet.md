# Developer Cheatsheet

## Using the VSCode Debugger with Frappe

You must have the python extension for VSCode for this to work.

Once you install it, go to "Open Configurations" from the "Run" menu
Select "Python" > "Python File" (You may have to open a python file to have the "Python" option appear
Add the below to the list :
> Make sure your workspaceRoot (The folder that's opened in VSCode) is the apps folder
```launch.json
        {
            "name": "Frappe",
            "type": "python",
            "request": "launch",
            "stopOnEntry": false,
            "pythonPath": "${config:python.pythonPath}",
            "program": "${workspaceRoot}/frappe/debug.py",
            "cwd": "${workspaceRoot}",
            "console": "integratedTerminal",
            "redirectOutput": true
        },
```

## Open VSCode Settings

- Set the PythonPath Variable to the absolute path of python **INSIDE** the frappe-bench `env`folder. For example, mine looks like this :

```
    "python.pythonPath": "/Users/vjfalk/frappe-develop/env/bin/python",
```

- Create a file in the frappe folder called `debug.py`
In the file, add the below :

```debug.py
import frappe.app
frappe.app.serve(port=<The port of your bench, usually 8000>, sites_path='<Absolute Path to the sites folder in your bench>')
```

- Edit the Procfile inside your bench, and **remove** the line that starts with **bench serve**
- Edit frappe/app.py, in the run_simple method, change use_reloader to False
- Edit your /etc/hosts and add an entry for the site(s) you want to debug (do this on host machine if using Remote Development Extensions for developing inside a VM)

## Run bench start in the terminal

Start debugging process in VSCode and wait for output to say Running on http://0.0.0...
Open your site by accessing it from your browser with the hostname you have set
If you see output in the "Debug Console" of VSCode, it's working!
DEBUG! 💃

[Using the VSCode Debugger with Frappe](https://github.com/frappe/frappe/wiki/Using-the-VSCode-Debugger-with-Frappe)

## Server Side

### frappe module

```frappe.form_dict```

Request parameters

```frappe.get_doc(doctype, name)```

e.g. frappe.get_doc('Project', 'My Project')

Load a document from the database with give doctype (table) and name Returns a Document object. All columns are properties. e.g. doc.name

```frappe.get_meta(doctype)```

Loads the metadata (DocType) of the given doctype.

e.g. frappe.get_meta('Task').fields is the list of fields in Task doctype

```frappe.get_all(doctype, filters, fields)```

e.g. frappe.get_all('Project', filters={'status': 'Open'}, fields=['name', 'description'])

Returns a list of dict objects from the database

```frappe.get_list(doctype, filters, fields, order_by)```

Sames as frappe.get_all, but will only show records permitted for the user

Allows sorting with the order_by parameter (optional). E.g. frappe.get_list('Payment Entry', filters={'docstatus': 0, 'payment_type': 'Pay'}, fields=['name', 'posting_date', 'paid_amount'], order_by='posting_date')

*Note* that the get_list command will return no more than 20 items by default. To increase this, use the limit_page_length parameter. Using the limit_start parameter allows to use pagination. In case the requested is a child table, do not forget to pass the parent parameter with the parent doctype to check permissions.

```frappe.get_value(doctype, name, fieldname)```

Return a single value from the database Example: frappe.get_value('Task', 'TASK00030', 'owner')

```frappe.get_last_doc(doctype)```

e.g. frappe.get_last_doc('Project')

Get last created document of this type.

```frappe.get_single(doctype)```

e.g. frappe.get_single('Dropbox Settings')

Return a frappe.model.document.Document object of the given Single doctype.`

```frappe.get_installed_apps()```

Returns a list of all the installed apps in the current site

### Document Object

#### Load a document

```python
doc = frappe.get_doc(doctype, name)

# get properties
doc.title

# set properties to the document
doc.first_name = 'My Name'

# save a document to the database
doc.save()
```

#### Insert a new doc

```python
doc = frappe.get_doc({
	"doctype": "Project",
	"title": "My new project",
	"status": "Open"
})
doc.insert()
```

### How to make public API

[REF](https://github.com/frappe/frappe/wiki/Developer-Cheatsheet)