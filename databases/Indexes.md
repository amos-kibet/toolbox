## Indexes

- They are a persistent data structure.
- They are the primary way of getting improved performance out of a database.
- In simple terminology, an index maps search keys to corresponding data on disk by using different in-memory and on-disk data structures.
- Mostly, an index is created on the columns specified in the `WHERE` clause of a query as the database retrieves and filters data from the tables based on those columns.
- There are two common data structures used for indexes:
  - **Balance tree**, or **B tree**, or **B+tree**
    - Used with conditions of the form "attribute equals value", "attribute less than value", "attribute between two values", etc.
    - Runs in logarithmic time.
  - **Hash tables**
    - Used for equality conditions, i.e., "attribute equals value".
    - Runs in constant time when properly designed.
- In its simplest form, an index is a sorted table that allows for searches to be conducted in O(Log N) time complexity using binary search on a sorted data structure.
- **Note:** Updating a table with indexes takes more time than updating a table without (because the indexes also need to be updated), so, only create indexes on columns that will be frequently searched against.
