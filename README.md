# Practical Database

This repository explains different database scenarios, helping us design better database architectures.

## I heard about Schemaless. MongoDB is Schemaless. Does that mean there is no schema?

No. There is.

A Relational database has a well-defined schema and enforces it when adding or creating resources.

In a Non-Relational (NoSQL) Database like MongoDB, there is no strict schema enforcement, allowing you to store data without a predefined structure. This means each document in a collection can have different fields, providing greater flexibility for data storage and modification.

MongoDB (No Schema Enforcement) Example

```
// Document 1
{ "id": 1, "name": "Lahin", "age": 31, "city": "Sylhet" }

// Document 2 (No 'age' field)
{ "id": 2, "name": "Meherun", "city": "Sylhet" }

// Document 3 (Additional field 'email')
{ "id": 3, "name": "Runa", "age": 26, "email": "runa@example.com" }
```

- The first document has, id, name, age, and city.
- The second document does not have an age field, but it's still valid.
- The third document adds a new field (email), which wasn’t defined before. It is also valid.

We can say like this,

A relational database follows a schema-on-write approach, meaning the schema is explicitly defined before data is inserted. Developers must update the schema before storing or interpreting new data.

Where a document database follows a schema-on-read approach, where the schema is implicit and interpreted only when the data is read or queried.
