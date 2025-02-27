# Database Uncovering the Q&A

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

## People say that SELECT \* is slow in terms of performance. Why?

When you fetch data from disk, it loads a page or multiple pages (if not indexed) from disk into the shared buffer pool (heap).

- Using SELECT \* retrieves all columns, whether they are indexed or not. Since every column is requested, the database must perform additional I/O operations to fetch each column from the shared buffer pool. Excessive I/O operations are costly and can impact performance.

- Another drawback of using SELECT \* is that the database must deserialize all columns, even those that may not be needed for your specific use case. This increases computational overhead and can slow down query performance.

- Modern database engines allow you to include non-key columns directly in an index, enabling the index to 'cover' more query requirements without accessing the heap.

Consider the following query:

```sql
SELECT title, description, genre FROM books WHERE genre = 'Science Fiction';
```

Here, genre is indexed (as a secondary index in InnoDB). However, it still needs to access the heap (the main table storage) to retrieve title and description. This can take time.

What if you add a Covering Index like this,

```sql
CREATE INDEX idx_covering ON books (genre, title, description);
```

Now, when we run the same query:

```sql
SELECT title, description, genre FROM books WHERE genre = 'Science Fiction';
```

MySQL or any other database can retrieve all required columns directly from the index without accessing the heap.

However, if the query has no covering index like idx_covering:

```sql
SELECT * FROM books WHERE genre = 'Science Fiction';
```

The database must access the heap to fetch all columns, even those not in the index. This increases I/O operations and slows down performance.

## When I delete a row, I get a "Foreign Key Constraint Failed" error message. Why?

Foreign Key Constraint ensures the integrity and consistency of data across related tables. These constraints specify the actions to be taken when a referenced record in the parent table is updated or deleted.

Two practical constraints are,

- CASCADE: When the referenced record in the parent table is updated or deleted, the corresponding foreign key values in the child table are automatically updated or deleted to match the new values in the parent table.

- SET NULL: When the referenced record in the parent table is updated or deleted, the foreign key values in the child table are set to NULL.

If you do not explicitly define a delete behavior (CASCADE, SET NULL), the default behavior is RESTRICT, meaning the database will prevent deletion if there are dependent records in the child table.

## I know that indexing can improve query performance. What is the best practice for adding index to a column? Should I make all columns index?

Answer is Cardinality. Cardinality generally refers to the uniqueness of data values in a particular column. For example, consider an orders table with the following attributes: id, customer_id, and status.

- id and customer_id have high cardinality because both are unique. As the table grows in size, the uniqueness of these columns increases, which means higher cardinality.

- status has low cardinality because it can only have a few possible values like "pending," "processing," or "delivered." Many rows may contain the same status.

When you index a high cardinality column, table scans are reduced, and finding a unique indexed value (like customer_id) becomes faster.

On the other hand, indexing a low cardinality column results in more table scans because the column is not unique, leading to less efficiency.

## He said to use id as auto-increment but she said to use id as UUID/ULID. I am confused.

Depends.

- auto-increment predictable. UUID/ULID not predictable.
- auto-increment size 4 bytes (32-bit integer) or 8 bytes (64-bit integer). UUID/ULID size 16 bytes.

Those are two major considerations. You can choose one or go for a hybrid approach.

## Why do people set the username or email as a UNIQUE constraint?

If you are designing a database schema (SQL), make sure to use the UNIQUE constraint to ensure that all values in a column are distinct.

Apply the UNIQUE constraint to columns that need to be unique, such as email and username, to maintain data integrity and accuracy by preventing duplicate values.

When you define a UNIQUE constraint on a column, the database automatically creates a unique index for it.

Although unique indexes and regular indexes share similarities, a unique index enforces uniqueness across all rows in a column, whereas a regular index does not.

You can apply the UNIQUE constraint to a single column or a combination of columns (multi-column). However, in MySQL, a multi-column UNIQUE constraint does not enforce bidirectional uniqueness—it only ensures that the combination of values in the specified columns is unique.

## When to use Soft Delete and Hard Delete?

Soft Delete and Hard Delete are critical aspects of the system, as they have an impact (negative or positive) in the future.

Before choosing an option, it is important to understand the use case and verify it with the business owner.

Use Soft Delete when, Data Recovery is necessary.

Use Hard Delete when, Data Recovery is not necessary.

## In terms of performance ULID works better than UUID. Explain.

Because ULID contains a 48-bit timestamp and an 80-bit random value, since the first part is a timestamp, it allows ULID to be lexicographically sorted. So for sorted nature, it works better with the B+ tree data structure(indexing) for finding, insertion, and delation operations.

## How can we efficiently process 500,000 rows from a different MySQL table every midnight while ensuring system stability, minimizing performance impact, and preventing Out of Memory (OOM) errors?

Now we can grab the 500,000 of rows which wasn't an issue. Now if we process all 500,000 amounts of rows together this will create some issues,

- loading large datasets together into memory can lead to Out of Memory errors, especially in environments with limited RAM.

- what if processing is interrupted (example, server crash, network issue, application failure), you might have partially processed data.

- if the underlying data in your table changes while you're processing, you might get inconsistent results. Strong consistency was required.

How can we overcome from these issues,

- Use Limit and Offset clauses in your SELECT statements to retrieve data in batches. This way we can avoid overwhelming the server by retrieving data in smaller chunks. Using offset may cause performance issues, use Primary Key (auto-increment).

- Message Queue: Put the batching info to the queue so that worker can pick it and process it. After finishing a process we can group those results and store that to the database table in a single row.

- Transaction: Wrap our batch processing within Transaction. So that if an interruption occurs, the transaction can be rolled back, preventing partial updates.

- Use transaction isolation levels like REPEATABLE READ or SERIALIZABLE to ensure that your queries see a consistent snapshot of the data throughout the entire processing period.
