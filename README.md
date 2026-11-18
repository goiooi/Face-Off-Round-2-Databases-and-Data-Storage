# Face-Off Round 2: Databases and Data Storage

## Topic 5.1: The Relational Core

This study note covers the fundamentals of relational databases, including tables, primary keys, foreign keys, referential integrity, cascading and normalisation.

The goal is to understand how relational databases organise information, maintain relationships and prevent inconsistent data.

---

## 1. What Is a Relational Database?

A relational database organises information into tables. Each table contains:

- **Rows:** Individual records.
- **Columns:** Attributes or fields describing those records.

Consider an online shop with two tables.

### Customers Table

| customer_id | name | email |
|---|---|---|
| 1 | Ama | ama@email.com |
| 2 | Kojo | kojo@email.com |
| 3 | Esi | esi@email.com |

### Orders Table

| order_id | customer_id | amount |
|---|---|---:|
| 101 | 1 | 250 |
| 102 | 2 | 180 |
| 103 | 1 | 400 |

The `customer_id` column appears in both tables. It connects each order to the customer who placed it.

This is the foundation of relational databases: storing related information in separate tables and connecting those tables through keys.

---

## 2. Primary Keys

A **primary key** uniquely identifies every row in a table.

For example, `customer_id` can serve as the primary key in the Customers table.

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(150)
);
```

A primary key has two important properties:

- Every value must be unique.
- It cannot contain `NULL`.

Two customers cannot have the same `customer_id`.

A person's name is not necessarily a good primary key because different people can have identical names.

**Remember:** A primary key uniquely identifies a record.

---

## 3. Foreign Keys

A **foreign key** is a column that refers to a key in another table. It establishes a relationship between tables.

In our example, `customer_id` in the Orders table refers to `customer_id` in the Customers table.

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(10, 2),

    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
);
```

This ensures that every order references a customer who exists in the Customers table.

For example:

```sql
INSERT INTO orders (order_id, customer_id, amount)
VALUES (104, 99, 300);
```

If customer `99` does not exist, the database rejects the insert when the foreign key constraint is enforced.

**Remember:** A foreign key connects related tables and helps prevent invalid references.

---

## 4. Referential Integrity

**Referential integrity** means that relationships between tables remain valid.

The database prevents situations such as:

- An order referencing a customer who does not exist.
- Deleting a customer while their orders still reference that customer, unless the deletion rule allows it.

This prevents orphan records.

### Cascading

Cascading defines what happens to related records when a referenced record is updated or deleted.

Example:

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(10, 2),

    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
        ON DELETE CASCADE
);
```

With `ON DELETE CASCADE`, deleting a customer also deletes that customer's related orders.

Cascading is useful, but it must be applied carefully because it can remove more data than intended.

---

## 5. Normalisation

**Normalisation** organises tables so that each fact is stored once.

Consider this poorly designed table:

| order_id | customer_name | customer_email | product |
|---|---|---|---|
| 101 | Ama | ama@email.com | Laptop |
| 102 | Ama | ama@email.com | Mouse |
| 103 | Kojo | kojo@email.com | Keyboard |

Ama's name and email are repeated for every order.

Suppose Ama changes her email address. Every row containing her details must be updated. If one row is missed, the database contains conflicting information.

This is called an **update anomaly**.

### A Normalised Structure

Separate the information into two tables.

**Customers**

| customer_id | name | email |
|---|---|---|
| 1 | Ama | ama@email.com |
| 2 | Kojo | kojo@email.com |

**Orders**

| order_id | customer_id | product |
|---|---|---|
| 101 | 1 | Laptop |
| 102 | 1 | Mouse |
| 103 | 2 | Keyboard |

Now Ama's details are stored once. Orders refer to her through `customer_id`.

### Benefits of Normalisation

- Reduces data duplication.
- Prevents update anomalies.
- Improves data consistency.
- Makes relationships easier to manage.

The trade-off is that joins are often needed to bring related information back together.

---

## 6. Practical SQL Exercises

Use the following SQL statements to create two tables.

### Create the Tables

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    student_name VARCHAR(100),
    email VARCHAR(150)
);

CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    student_id INT,
    course_name VARCHAR(100),

    FOREIGN KEY (student_id)
        REFERENCES students(student_id)
);
```

### Insert Sample Records

```sql
INSERT INTO students
VALUES
(1, 'Ama', 'ama@email.com'),
(2, 'Kojo', 'kojo@email.com'),
(3, 'Esi', 'esi@email.com');

INSERT INTO courses
VALUES
(101, 1, 'Database Systems'),
(102, 2, 'Networking'),
(103, 1, 'Programming');
```

### Your Tasks

Try completing these exercises without looking up the answers.

1. Insert a student with ID `4`, name `Yaw` and email `yaw@email.com`.
2. Add a course for student ID `3`.
3. Try adding a course for student ID `99`. What should happen, and why?
4. Explain why `student_id` is a primary key in the Students table but a foreign key in the Courses table.
5. Explain one problem that normalisation prevents.

## 7. Face-Off-Style Questions

Test your understanding with these questions.

### Question 1

Which property must a primary key satisfy?

A. It must contain unique, non-NULL values.

B. It must contain only numbers.

C. It must be the same as the foreign key.

D. It can contain duplicate values.

### Question 2

What does a foreign key do?

A. Encrypts a table.

B. Connects a table to a referenced key in another table.

C. Sorts records alphabetically.

D. Creates a backup.

### Question 3

What happens if an order references a customer ID that does not exist?

A. The database creates the customer automatically.

B. The order is accepted with a blank customer.

C. The database rejects the insert when the foreign key is enforced.

D. The database changes the customer ID.

### Question 4

What problem does normalisation help prevent?

A. Network latency.

B. Update anomalies caused by duplicated facts.

C. Slow internet connections.

D. Incorrect passwords.

### Question 5

What does `ON DELETE CASCADE` do?

A. Prevents every deletion.

B. Deletes related rows when the referenced row is deleted.

C. Copies the deleted row.

D. Changes the primary key.


## 8. Quick Revision Notes

| Concept | Key Point |
|---|---|
| Relational database | Organises data into related tables |
| Primary key | Uniquely identifies a row and cannot be NULL |
| Foreign key | References a key in another table |
| Referential integrity | Prevents invalid relationships |
| Cascade | Applies an action to related records |
| Normalisation | Stores each fact once |
| Update anomaly | Conflicting data caused by inconsistent updates |

### Key Takeaways

- Primary keys identify records.
- Foreign keys establish relationships.
- Referential integrity protects those relationships.
- Cascading controls what happens to related records.
- Normalisation reduces duplication and update anomalies.

## 9. Study Checkpoint

Before moving to the next topic, make sure you can:

- [ ] Explain what a relational database is.
- [ ] Distinguish primary keys from foreign keys.
- [ ] Explain referential integrity.
- [ ] Describe how `ON DELETE CASCADE` works.
- [ ] Explain normalisation and update anomalies.
- [ ] Complete the five SQL exercises.
- [ ] Answer the five Face-Off-style questions.
---

*Study material based on Section 5.1 of the official Face-Off Round 2 brief.*
