## Composite Indexes in PostgreSQL

Also called multicolumn indexes, combined indexes, or concatenated indexes.

A composite index can have a maximum of 32 columns of a table. The limit can be changed by modifying the `pg_config_manual.sh` when building PostgreSQL.

Only `B-tree`, `GIST`, and `BRIN` Index types support multicolumn indexes.

The following syntax shows how to create a composite index:

```sql
CREATE INDEX index_name ON table_name(a, b, c, ...);
```

\*\*More Example

First, let's create a table, `people`, with three columns; `id`, `first name`, and `last name`:

```sql
CREATE TABLE people(
    id INT GENERATED BY DEFAULT AS IDENTITY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL
);
```

**Important:** The PostgreSQL Optimizer uses the indexes for table for the columns which have been defined in the `where` clause in the `SQL` statement. Example:

```sql
SELECT * FROM people WHERE last_name = 'Adams' AND first_name = 'Lou'
```

In the above `SQL` statement, you should define a composite/multicolumn index for `first_name` and `last_name` columns.

**NOTE:** Order matters when listing the columns under one composite index. As a general rule, always put the columns that are frequently looked up at the top, followed by the less frequently looked up ones.