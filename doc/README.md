## About this folder
This folder shows qualifiers separated from the External Data Source for convenience and instructional purposes. The qualifier syntax shown for each object shows a variety of useful examples to help you in your own projects, and the notes below call out what is unique about each object that might be worth your attention.

Drill into the files above to see the JSON syntax, and then refer to the information below for additional insight on why that syntax is needed or beneficial.
---

### Order
- The partition key (`pk` attribute) is a String formatted like an integer (e.g. `10594`). For the purposes of Salesforce Connect, it is **strongly** recommended that primary keys be Strings. The regex `^[0-9]+$` is used to find the "integer", which is combined with the static `sk` value `ORDER` to identify a given item in DynamoDB as an Order.
  - DynamoDB supports the use of partition key and sort key together as a composite key to uniquely identify an item. When both attributes are present, Salesforce Connect concatenates them with a hyphen to create the External ID on the External Object. However, for convenience, when an object's qualifier specifies that the sort key is a static value (e.g. `ORDER`), the External ID will *only* consist of the partition key value. So, here, instead of `10594-ORDER`, the External ID is simply `10594`.
  - It is also possible to create a DynamoDB table with *only* a partition key and no sort key. In that case the External ID will be equal to the value of the partition key.
- Formulas are used to convert String attributes in DynamoDB into Date and Number fields in Salesforce.
  - For example, `shippedDate` is converted via the `DATEVALUE()` function, and `freight` is converted into a Number via `VALUE()`.

### Order Detail
- This object is modeled in DynamoDB using the [adjacency list](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-adjacency-graphs.html) pattern, which is a popular way of representing many-to-many relationships in DynamoDB. The `pk` contains the ID of the parent Order (e.g.`10594`), and the `sk` contains a compound value representing the Product ID (e.g. `products#23`). Regular expressions are used to identify that pk/sk pattern and separate the rows in DynamoDB that are "line items" from the Orders, Products, etc.
  - For instructional purposes, a formula is used to decode that compound value and parse out the Product ID as a `virtual` attribute called `productCode`. A `virtual` attribute is not physically present in DynamoDB, but can be used as though it were. An administrator could use that to create an External Lookup field to a Product object, using `productCode` as the External Column Name. 
  - Said another way, the formula removes the prefix and "cleans up" the raw data from the external system, and administrators can use the "clean" value as if it existed in the remote data set. Compound attribute values are relatively common in DynamoDB, so this is a useful solution pattern.
- Similar to the Order object, the Order Detail leverages the `VALUE()` and `TEXT()` functions in formulas to convert String attributes into Numbers in Salesforce.
  - Note that, ideally, the Discount field would be represented as a Percentage in Salesforce. Percentage fields must be mapped to a Number in DynamoDB, however, and since the source attribute is a String, it is represented as a Number field.

### Product
- Similar to Order Detail, this qualifier uses the `VALUE()` function to convert the String data in DynamoDB to a numeric value that can map to a Number field in Salesforce. Each Number field has a corresponding formula that uses the `TEXT()` function to convert the number _back_ to a String so it can be stored in DynamoDB.
  - Note that this second re-encoding formula is needed when users in Salesforce need to *edit* the data. From the perspective of Salesforce, they are entering a Number, and it needs to be converted to a String (in this example) for DynamoDB.

### Shipper
This object deviates from the modeling approach in [the prerequisite](https://github.com/trek10inc/ddb-single-table-example) for the sake of illustrating certain concepts relevant to Salesforce administrators.
- The name of the company is moved from the sort key to a specific `companyName` attribute to allow the name to be edited without the cost of another transaction.
  - Primary keys in DynamoDB are immutable, and attempts to update them from Salesforce will generate an error. You can think of the primary keys like the 15 or 18 character internal Salesforce IDs on a native record.
- The sort key is populated with the static value `SHIPPER` to allow for simpler External IDs in Salesforce.
- The `expeditingCharge` Number attribute is added as an illustration of how to render a Currency value in Salesforce. DynamoDB does not have a native data type for currency, so the currency is inferred in Salesforce by either the org-wide currency or the `currencyIsoCode` of the record (in the case of multi-currency). Note that no qualifier is needed.
- The `expeditedShipping` Boolean attribute is added as an illustration of how to render a Checkbox in Salesforce. Note that no qualifier is needed.

### Supplier
- This object makes extensive use of parsing formulas to decode an address stored in a single attribute in DynamoDB. The `data` field captures the street address as `country#region#city#address` for querying purposes (e.g. `USA#MI#Ann Arbor#707 Oxford Rd.`).
  - Data would be stored in this format to be queried using [the `BEGINS_WITH` function in PartiQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ql-functions.beginswith.html). Since this is beneficial to DynamoDB developers, it is a relatively common pattern that Salesforce administrators may encounter.

### Employee
The `birthDate` column is altered from the original example to show the use of `DATETIMEVALUE()` to map to a Date/Time field in Salesforce. [Amazon recommends](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html) such data be stored as a String attribute in [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) format (e.g. `2015-12-21T17:42:34Z`).

### Category
Category is included for completeness, though the fact that the sort key contains the category name creates various issues for Salesforce Connect. The qualifier here shows how the data can be decoded, but it would likely be more advantageous to take the approach described above for the Shipper object.

### Account
Account is a native object and has no qualifier, but is included here for convenience to round out the example. Deploying the metadata in this repo will add a Customer ID field to Account that can be populated for the Indirect Lookup from Order to Account.