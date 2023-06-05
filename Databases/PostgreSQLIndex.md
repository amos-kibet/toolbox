## PostgreSQL Indexes

An index is a separate data structure, e.g., `B-tree`, that speeds up the data retrieval on a table at the cost of additional writes and storage to maintain it.

They are effective tools to enhance database perfomance. Indexes help the database server find specific rows much faster than it could do without them.

However, indexes add write and storage overheads to the database system, and it is important to know how to use them appropriately.

**Example of how to create an index:**

### Index Types

PostgreSQL has several index types:

- B-tree
- Hash
- GiST
- SP-GiST
- GIN
- BRIN

Each index type uses a different storage structure and algorithm to cope with different kinds of queries.

When you use the `CREATE INDEX` statement without specifying the index type, PostgreSQL uses `B-tree` index type by default because it is best fit the most common queries.
