# Form Design Guidelines

Please keep these rules in mind while designing forms

1. Proximity
Fields related should be listed in columns within the section break. For example: Customer and below Tax ID and when no more room for the column should jump to the next column.

If the visibility of a field is dependent on another field, it must ideally be below the field.

2. Important and Mandatory Fields on the Top and Left
Important and mandatory fields should be on the top and left whenever is possible and after applying the proximity rule. This is the area that gets maximum attention of the user. If the mandatory fields are in the bottom, users may not scroll all the way down and then will get an error, making them go back and fix something.

3. Section Must Have Less Than Six Fields
If your section is having more than 6 fields, try and group them into a separate section. Sections promote hierarchy, that make the form look organized and make it easy for users to skim through the data they are looking for.

4. Set Default Values
Keep the number of mandatory fields you want the user to enter at an absolute minimum. Please use defaults, either global, or in context of the workflow.

5. Dashboard of Related Items
If your document is related to other items, the Form Dashboard must show a list of all related documents in a grouped manner. This will enable contextual discovery and navigation, and the user will be able get a quick understanding of what is the relation of this document related to the rest of the system.

6. Every Section Must Have a Heading
If your section does not have an heading, it will be considered an extension of the previous section. Section heads help the form look organized.

7. Avoid Using Buttons Inside the Form
Avoid the pattern where you will "fetch" data based on a button as much as possible. This must be done automatically on selection. If you need buttons, then put them on the toolbar.

8. Transactions must be Auto Numbered
Only documents that represent things that have names in the real world should have user created names, for the rest, the document name must be an auto-number. For example an Invoice

9. All documents must have a dashboard or contextual links
No piece of information lives by itself, it is always connected to some other piece of information. Having contextual navigation makes it easier for the user to comprehend how this document lives in context to other documents. A dashboard can be added by making a _dashboard.py file in the doctype folder

Example: Dashboard for holiday list

def get_data():
	return {
		'fieldname': 'holiday_list',
		'non_standard_fieldnames': {
			'Company': 'default_holiday_list',
			'Leave Period': 'optional_holiday_list'
		},
		'transactions': [
			{
				'items': ['Company', 'Employee', 'Workstation'],
			},
			{
				'items': ['Leave Period', 'Shift Type']
			}
		]
	}