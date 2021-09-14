# Export Custom field using export fixtures

Hello, community, You can add the custom field using two approaches one is using make_custom_field() function or other is using fixtures. If you added a custom field to the standard module using customize the form. It will remain to that local or cloud instance if you want to use these changes on other instance when you use csv or use the backup to restore changes but you can create a custom app. In custom app hooks.py add the following line

fixtures = [
       {
         "dt": "Custom Field", 
         "filters":[["name", "in", ['Sales Invoice-section_break_20','Sales Invoice-delivery_note','Sales Invoice- 
              supplier_ref', 'Sales Invoice-buyers_order_no', 'Sales Invoice-despatch_document_no','Sales Invoice- 
              despatched_through', 'Sales Invoice-country', 'Sales Invoice-delivery_note_date', 'Sales Invoice- 
              other_references','Sales Invoice-terms_of_delivery', 'Sales Invoice-column_break_28','Sales 
              Invoicebuyers_order_date', 'Sales Invoice-mode_terms_of_payment','Sales Invoice-destination', 'Sales 
              Invoice-e_way_bill']]]
      },
      {
        "dt": "Print Format", 
        "filters": [["name", "in", ["challan_cum_tax_invoice"]]]
      }
]
In custom filed 'Doctype-custom_doc_field' after that try bench --site site_name export-fixtures It will create fixture table in your custom app so that you can easily installed on another environment or other instance without taking csv of customize form.

Syntax
The data structure is as follows: fixtures is a list of dictionary_objects:

fixtures = [object1, object2, objectN]

Each dictionary_object with filters contains:

*dictionary_object* = { "doctype":"*doctype_name*", "filters":"*list_of_filters*" }

doctype is self-explanatory, it is the DocType name you wish to export.

Each list_of_filters contains itself, a list_of_filter_criteria The list_or_filter_criteria itself is a list containing:

*fieldname*, *operator*, (*value*)

Note that value is a Tuple!

*list_of_filter_criteria* = ["fieldname", "operator", ("Value1","Value2","ValueN", )]

So that: "filters": [list_of_filter_criteria]

For your convenience, these are the allowed operators:

Side note: I stumbled upon the operators during a traceback while running bench export-fixtures by using "not" and this was the message returned:

Operator must be one of =, !=, >, <, >=, <=, like, not like, in, not in, between, descendants of, ancestors of, not descendants of, not ancestors of, is```



[Export Custom field using export fixtures](https://github.com/frappe/erpnext/wiki/Export-Custom-field-using-export-fixtures)
