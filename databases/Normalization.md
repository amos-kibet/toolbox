## Database Normalization

- Normalization is a method of organizing the data in the database which helps you to avoid data redundancy, insertion, update & deletion anormaly.
- It is a database design technique that reduces data redundancy and eliminates undesirable characteristics like insertion, update and deletion anormalies.
- It is a process of analyzing the relation among schemas based on their different functional dependencies and primary key.
- Normalization rules divide larger tables into smaller tables and link them using relationships.

### Common Database Normalization Forms

**1NF (First Normal Form)**

First normal form states that at every row and column intersection in the table, there exists a single value, and never a list of values. For example, you cannot have a field named `Price` in which you place more than one price.

**2NF (Second Normal Form)**

Second normal form requires that each non-key column be fully dependent on the entire primary key, not on just part of the key. This rule applies when you have a primary key that consists of more than one column. For example, suppose you have a table containing the following columns, where `Order ID` and `Product ID` form the primary key:

- Order ID (primary key)
- Product ID (primary key)
- Product Name

This design violates second normal form, because Product Name is dependent on Product ID, but not on Order ID, so it is not dependent on the entire primary key. You must remove `Product Name` from the table. It belongs in a different table (`Products`).

**3NF (Third Normal Form)**

Third normal form requires that not only every non-key column be dependent on the entire primary key, but that non-key columns be independent of each other.

Another way of saying this is that each non-key column must be dependent on the primary key and nothing but the primary key. For example, suppose you have a table containing the following columns:

- ProductID (primary key)
- Name
- SRP
- Discount

Assume that `Discount` depends on the suggested retail price (`SRP`). This table violates third normal form because a non-key column, `Discount`, depends on another non-key column, `SRP`. Column independence means that you should be able to change any non-key column without affecting any other column. If you change a value in the `SRP` field, the `Discount` would change accordingly, thus violating that rule. In this case `Discount` should be moved to another table that is keyed on `SRP`.

### Othe Database Normalization Forms

- BCNF (Boyce-Codd Normal Form)
- 4NF (Fourth Normal Form)
- 5NF (Fifth Normal Form)
- 6NF (Sixth Normal Form)
