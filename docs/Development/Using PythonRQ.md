#Using Python RQ

In yourapp/tasks.py:

>Note: No need for the site parameter. This function is called after init and connect!

```python
def do_something(param1, param2, ...):
    try:
        ...

    except:
        frappe.db.rollback()
        ...

    else:
        frappe.db.commit()

    # no need to call frappe.destroy() in finally, as this is handled by frappe
    # in fact, the whole try except else is optional if you are not doing any exception handling
```

and its call somewhere

```python
from yourapp.tasks import do_something
from frappe.utils.background_jobs import enqueue
...

def some_method():
    ...
    enqueue(do_something, param1=param1, param2=param2, ...)
```

Also, it is not required for you to keep the do_something method in yourapp/tasks.py anymore. You can keep it in any importable file and import it at the top.

You can also pass timeout in seconds to enqueue