## Transactions

- A transaction is a sequence of one or more SQL operations treated as a unit.
- Transactions appear to run in isolation.
- If the system fails, each transaction's changes are reflected either entirely or not at all.
- Every database transaction ends in either a **commit** or a **rollback**, where a **commit** is a successful operation, and **rolloback** is a failed operation. So, the database state will either be in the updated (commit) state, or initial (rollback) state after every transaction.
- Transactions ensures that a database conforms to the **ACID** principles:
  - **Atomicity** - All operations within a transaction complete successfully, or do not complete at all.
  - **Consistency** - Any transaction leaves the database in a consistent state, regardless of the outcome of the transaction.
  - **Isolation** - Each transaction has access to an isolated version of the database. Data that has not yet been completed cannot be modified by other transactions.
  - **Durability** - Once a transaction has been completed successfully, its effect will remain in the database even if the database fails. Thus, if a transaction is completed but the database crashes before writing data to disk, the data will be updated when the system returns to service.

### How to define a database transactions

In PostgreSQL and MySQL these keywords are used: `START TRANSACTION` and `COMMIT`. In SQL server these are: BEGIN TRANSACTION``and`COMMIT TRANSACTION`.

```SQL

-- initializing the transaction
START TRANSACTION;
-- adding 100 points to user 1 and 5
UPDATE users
SET points = points + 100
WHERE id IN (1, 5);
-- removing 200 points from user 4

UPDATE users
11
SET points = points - 200

WHERE id = 4;

-- commiting the change (or rolling it back later in case of failure)

COMMIT;
```

### States of a database transaction

In the case of a transactional database, the lifecycle of a transaction can be described by the following four steps:

1. **The transaction begins**: The transactional database prepares everything required to execute the transaction,
2. **The queries defined in the transaction are executed**: This is when data manipulation takes place,
3. **If no errors occur, the transaction is committed**: The transaction ends successfully,
4. **If an error occurs, it rolls back the transaction**: The transaction ends with failure and any query executed before it failed is reversed.

### Referrences

1. https://www.dbvis.com/thetable/database-transactions-101-the-essential-guide/#:~:text=A%20database%20transaction%20is%20a,operations%20performed%20within%20a%20DBMS.
