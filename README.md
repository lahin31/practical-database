# Practical Database

This repository explains various database concepts through real-world scenarios, providing insights that help in designing more efficient and scalable database architectures.

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

## I am using MySQL, and I have seen some people use DATETIME while others use TIMESTAMP. What is the difference?

DateTime:

- Stores date and time (from '1000-01-01 00:00:00' to '9999-12-31 23:59:59').

- Use DateTime when you care about a specific time, like 'appointment','schedule_at', 'exam_start', etc.

- If you use DateTime in this, you will get some native methods like DATE_ADD.

- The database stores the datetime data so that humans can read and understand it.

TimeStamp:

- Stores date and time (from '1970-01-01 00:00:01'UTC to '2038-01-19 03:14:07' UTC).

- TIMESTAMP is used to track changes in records and update them every time they are changed, or, in other words, 'created_at', 'updated_at', or 'deleted_at'.

- TIMESTAMP internally converts a current time zone to UTC for storage, and during retrieval, it converts back to the current time zone (DateStamp can't do that).

- The database stores the time stamp data in integer representation.

## What will happen to timestamps after 2038, since the limit is 2038-01-19 03:14:07?

It will be crushed. MySQL 8+ addressed the issue with UNIX_TIMESTAMP(). For that, MySQL should run on a 64-bit system.

## Data Integrity ensures us all the data in the database can be uniquely traced and connected to other table attribute. Explain.

There are 3 kinds of Data Integrity,

- Entity Integrity: This means each entity can be uniquely identified, with the help of Primary Key we can easily traced. This can be an Integrity Issue if we can't traced an entity.

- Referential Integrity: If two table colums are connected with each other we can call this Referential Integrity. With Foreign Key we can easily ensure the Referential Integrity. This can be an Integrity Issue if one entity is deleted which is connected to another table entity.

- Domain Integrity: This ensures that all the attributes contains correct data. For example, username table can't contain mobile number, this can also be an Integrity Issue.
