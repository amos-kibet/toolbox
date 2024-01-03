## Database Design Principles

A well-designed data model must support database integrity, perfomance, scalability, and security.

Following best practices for data modelling allows a database to meet a series of key objectives:

- **Integrity** - Observing principles such as non-redundancy and the use of constraints, primary and foreign keys, data validation, and referential integrity ensures that the information stored in the database maintains its integrity and consistency.
- **Performance** - Normalization principles optimize data access and eliminate redundancy. In turn, indexing principles contribute to speeding up result times.
- **Scalability** - A database must be able to grow without compromising its performance and the integrity of its data. The same design principles that contribute to integrity and performance also make scalability possible.
- **Security** - Database design principles aimed at controlling access to information – as well as encrypting and protecting sensitive data – make information security possible.
  **Maintanability** - Using naming conventions and maintaining up-to-date documentation ensure that databases are easy to use, update, and modify.

### Key Database Design Principles

**1. Avoid Redundancy**

- Redundant information in a database schema can lead to inconsistencies: if the same data is stored in multiple tables, there is a risk that it will be updated in one table and not in the others – generating discrepancies in the results.
- Redundancy also unnecessarily increases the storage space required by the database and can negatively affect query performance and data update operations. Normalization is used to eliminate redundancy in database models.
- An exception to the principle of non-redundancy occurs in dimensional schemas, which are used for analytical processing and data warehousing. In dimensional schemas, a certain degree of data redundancy can be used to reduce complexity in combined queries. Otherwise, such queries would be more resource intensive, by way of joins.

**2. Primary Keys and Unique Identifiers**

- Every table must have a primary key.
- The primary key guarantees uniqueness of each row within the table.
- It is also used to establish relationships with other tables in the database.
- Without a primary key, a table would have no reliable way to identify individual records. This can lead to data integrity problems, issues with query accuracy, and difficulties in updating that table. If you leave a table without a primary key in your schema, you run the risk of that table containing duplicate rows that will cause incorrect query results and application errors.

**3. Null Values**

- In relational databases, null values indicate unknown, missing, or non-applicable data.
- When defining each column in a table, you must establish whether it supports null values. You should only allow the column to support null values if you’re certain that, at some point, the value of that column may be unknown, missing, or not applicable.

**4. Referential Integrity**

- Referential integrity guarantees that columns involved in the relationship between two tables (e.g. primary and foreign keys) will have shared values. This means that the values of the columns in the secondary (child or dependent) table must exist in the corresponding columns of the primary (parent) table.
- Furthermore, referential integrity requires that such column values are unique in the primary table – ideally, they should constitute the primary key of the primary table. In the secondary table, the columns involved in the relationship must constitute a foreign key.

**5. Atomiticy**

- This ensures that at every intersection between row and column, there exists only one value, and never a list of values.
- It is guaranteed by 1st normal form.
- Example, storing user's full_name in one field is a violation. Instead, you should have separate fields for first_name and last_name respectively.
- If you combine more than one piece of information in one column, it will be difficult to access individual data later. For example, what if you wanted to address the customer by their first name? The query would get unnecessarily complicated.

**6. Normalization**

- In any data model for transactional applications or processes – such as an online banking or e-commerce site – it is crucial to avoid anomalies in the processes of inserting, updating, or deleting data.
- This is where normalization techniques are applied; they seek to eliminate clutter, disorganization, and inconsistencies in data models.
- In practice, normalizing a model is a matter of bringing it to the third normal form (**3NF**).
- Bringing a data model to 3NF maintains data integrity, reduces redundancy, and optimizes the storage space occupied by the database. The lower normal forms (**1NF** and **2NF**) are intermediate steps towards 3NF, while the higher normal forms (**4NF** and **5NF**) are rarely used.

**Reference:** [Vertabelo](https://vertabelo.com/blog/database-design-principles/)
