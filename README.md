# northwind-dynamodb-salesforce
This is intended to help Salesforce administrators and developers understand how to optimize External Objects that access data stored in [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html). We use the well-known Northwind eCommerce sample for instructional purposes to see how a relational data model might be stored in a single table in DynamoDB, and how to unpack that table into multiple External Objects in Salesforce.

### Prerequisites
This example references [another repository](https://github.com/trek10inc/ddb-single-table-example) and its related [blog post](https://trek10.com/blog/dynamodb-single-table-relational-modeling/) that helps people understand [the differences in data modeling](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-modeling-nosql.html) and provides scripts to populate DynamoDB. 

To walk through this example in its entirety, do the following:

- Step through [the sample in the other repo](https://github.com/trek10inc/ddb-single-table-example) to populate DynamoDB with the relevant data.
- Create an External ID text field on the Account object called `customerID`.
- Import the data in [customers.csv](https://github.com/trek10inc/ddb-single-table-example/blob/master/csv/customers.csv) as Accounts and Contacts using the [Data Import Wizard](https://trailhead.salesforce.com/en/content/learn/projects/import-and-export-with-data-management-tools/use-the-data-import-wizard), being careful to import the first column into the new `customerID` field.
- Create External Objects for the different entities referenced here…Orders, Products, etc.
  - You can import these samples directly with our [developer tools](https://developer.salesforce.com/developer-centers/developer-experience) or the Metadata API. If you don't need all that, you can create one or two objects by hand and experiment with the qualifiers.

### Qualifiers
Qualifiers are used to specify additional details about a remote data table that Salesforce Connect needs to unpack it appropriately. This repo contains a number of examples of qualifiers to read and write data stored in Amazon DynamoDB. 

In terms of Salesforce metadata, the qualifier syntax is stored with the External Data Source, though it is encoded/escaped and hard to read in the XML. Hence, for instructional purposes, it is separated out [in the doc folder](https://github.com/rossbelmont/northwind-dynamodb-salesforce/tree/main/doc), even though the files in that folder cannot be directly deployed to a Salesforce org. 

There is also [commentary in the README of the doc folder](https://github.com/rossbelmont/northwind-dynamodb-salesforce/tree/main/doc#readme) that discusses what is interesting or relevant about each object.