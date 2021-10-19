# Naming Guidelines

Naming something is probably the most important decisions that you need to take as a user. Here are some guidelines will selecting names

>"To change this rock into a jewel, you must change its true name. And to do that, my son, even to so small a scrap of the world, is to change the world" - Wizard of Earthsea

## General Guidelines

Naming should be something that is used in common language. There are legacy ERP systems that have a specific naming style, we should constantly keep *away* from those styles, going with a more simple naming style.
The Shopify is a great example of how to naming a things. For example, in legacy ERP systems, a Purchase Receipt is called as "Goods Receipt Note".

- Names should represent the essence of the concepts they represent and avoid accounting/management jargon.
- Names should be consistent with the rest of the system.
- No abbreviations. Use complete names as far as possible in both object and variable naming.

## DocType naming

    DocType names must be:

- Title Case
- Singular
- Names with multiple words should be separated by a space. Example "Sales Invoice"
- Use US English
- Child table names should be parent table names + the relation, for example, "Sales Order Item"

## Field Naming

- Labels must be Title Case
- Link names must be same as the DocType they refer to (for example Link to "Employee" should be employee)
- Table field names must be plural and just be the relation, for example, field name for "Sales Order Item" should be items
- Field name must be the slugged version of the label ("First Name" must be first_name)
- Avoid descriptions. Labels must be as clear as possible.
- Use full names (not abbreviations)

## Variable Naming

- Variables should representing Document objects must be *slugged* versions of the DocType [example: sales_order = frappe.get_doc('Sales Order', 'SO-0001')]
- Variables representing names, should be suffixed with _name [example: sales_order_name]
- Child table in a loop can be d [example: for d in sales_order.items]
- Avoid using numbered variables, like i1, i2 etc. Use descriptive variables.

[Naming Guidelines REF:](https://github.com/frappe/erpnext/wiki/Naming-Guidelines)