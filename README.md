# northwind-dynamodb-salesforce
This is intended to help Salesforce administrators and developers understand how to optimize External Objects that access data stored in [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html). We use the well-known Northwind sample for instructional purposes to see how a relational data model might be stored in a single table in DynamoDB, and how to unpack that table into multiple External Objects in Salesforce.
### Prerequisites
This example references [another repository](https://github.com/trek10inc/ddb-single-table-example) and its related [blog post](https://trek10.com/blog/dynamodb-single-table-relational-modeling/) that helps readers understand [the differences in data modeling](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-modeling-nosql.html) and provides scripts to populate DynamoDB. 

To walk through this example in its entirety, do the following:

- Step through [the Northwind sample](https://github.com/trek10inc/ddb-single-table-example) and populate DynamoDB with the relevant data.
- Create an External ID text field on the Account object called `customerID`.
- Import the data in [customers.csv](https://github.com/trek10inc/ddb-single-table-example/blob/master/csv/customers.csv) as Accounts and Contacts using the [Data Import Wizard](https://trailhead.salesforce.com/en/content/learn/projects/import-and-export-with-data-management-tools/use-the-data-import-wizard), being careful to import the first column into the new `customerID` field.
- Create External Objects for the different entities referenced here…Orders, Products, etc.
  - You can import these samples directly with our [developer tools](https://developer.salesforce.com/developer-centers/developer-experience) or the Metadata API. If you don't need all that, you can create one or two objects by hand and experiment with the qualifiers.