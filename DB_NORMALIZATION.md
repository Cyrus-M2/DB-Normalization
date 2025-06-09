# Database Normalization

<!-- ## Normalization in DBMS -->

### Purpose of Normalization
- Eliminate data redundancy
- Prevent anomalies:
  - **Insertion Anomaly**: Can't insert data without other required fields
  - **Update Anomaly**: Inconsistent updates across duplicate data
  - **Deletion Anomaly**: Unintended data loss when deleting records

### Example: Unnormalized Employee Table
| emp_id | emp_name | emp_address | emp_dept |
|--------|----------|-------------|----------|
| 101    | Rick     | Delhi       | D001     |
| 101    | Rick     | Delhi       | D002     |
| 166    | Glenn    | Chennai     | D004     |

**Problems**: 
- Rick's address appears twice (redundancy)
- Deleting D002 might accidentally remove Rick's record
- Can't add an employee without a department

---

## Normal Forms
##### 1NF (First Normal Form)
##### 2NF (Second Normal Form)
##### 3NF (Third Normal Form)
##### BCNF (Boyce & Codd normal Form)


### 1NF (First Normal Form)
**Rules**:
1. An attribute (column) of a table cannot hold multiple values
2. It should hold atomic values

**Example Violation**:
| emp_id | emp_mobile |
|--------|------------|
| 102    | 8812121212, 9900012222 |

**1NF Solution**:
| emp_id | emp_mobile |
|--------|------------|
| 102    | 8812121212 |
| 102    | 9900012222 |

---

### 2NF (Second Normal Form)

#### Conditions:
- Must be in 1NF
- No non-prime attribute is dependent on the proper subset of any candidate key of table.

#### Example: Teacher-Subject Database

**This Table (Violates 2NF):**

| teacher_id | subject  | teacher_age |
|------------|----------|-------------|
| 111        | Maths    | 38          |
| 111        | Physics  | 38          |
| 222        | Biology  | 38          |

#### Problems:

   -  **Redundancy**: Teacher age repeats for each subject
   -  **Update Anomaly**: Changing age requires multiple  updates
   -  **Partial Dependency**: teacher_age depends only on teacher_id (a subset of the candidate key {teacher_id, subject})

   #### Soultion for the above table
   **Teacher Details**
| teacher_id | teacher_age |
|------------|-------------|
| 111        | 38          |
| 222        | 38          |

**Teacher Subjects:**
| teacher_id | subject  |
|------------|----------|
| 111        | Maths    |
| 111        | Physics  |


### 3NF (Third Normal Form)

#### **Conditions**:
1. Must be in 2NF
2. Tansitive functional dependency of non-prime attribute on any super key should be removed.

#### Transitive Dependency in DBMS

##### Definition
A **transitive dependency** occurs when a functional dependency is indirectly formed through another attribute. 

Given attributes X, Y, and Z:  
`X → Z` is a transitive dependency if:
1. `X → Y` 
2. `Y ↛ X` (Y does not determine X)
3. `Y → Z`

##### Key Characteristics
- Requires **at least three attributes** in a relation
- Primary target for elimination in **Third Normal Form (3NF)**
- Creates data redundancy and update anomalies

#### Example
**Consider a book database:**
| Book           | Author              | Author_Age |
|----------------|---------------------|------------|
| Game of Thrones| George R.R. Martin  | 66         |
| Harry Potter   | J.K. Rowling        | 49         |

**Functional Dependencies:**
1. `Book → Author` 
2. `Author ↛ Book` (Multiple books can have same author)
3. `Author → Author_Age`

This creates a transitive dependency:  

`Book → Author_Age`

#### Normalization Solution (3NF)
To eliminate:
1. Create separate tables for directly dependent attributes
2. Establish relationships through foreign keys

**Normalized Structure:**

**BOOKS TABLE**:
| Book           | Author              |
|----------------|---------------------|
| Game of Thrones| George R.R. Martin  |
| Harry Potter   | J.K. Rowling        |

**AUTHORS:**
| Author              | Author_Age |
|---------------------|------------|
| George R.R. Martin  | 66         |
| J.K. Rowling        | 49         |


### This Table (Violates 3NF):

| emp_id | emp_zip | emp_city | emp_district |
|--------|---------|----------|--------------|
| 1001   | 282005  | Agra     | Dayal Bagh   |

#### Problems:
- `emp_zip → emp_city` creates a transitive dependency (`emp_id → emp_zip → emp_city`)

#### Solution for the above table:

**Employees:**

| emp_id | emp_zip |
|--------|---------|
| 1001   | 282005  |

**ZIP Codes:**

| emp_zip | emp_city | emp_district |
|---------|----------|--------------|
| 282005  | Agra     | Dayal Bagh   |

---

### Boyce-Codd Normal Form (BCNF)

#### **Conditions**:
1. Must be in 3NF
2. Every functional dependency `X -> Y`, where X should be the super key of the table.

#### **This Table (Violates BCNF):**

| emp_id | emp_dept | dept_manager |
|--------|----------|--------------|
| 1001   | D001     | John Smith   |

#### Problems:
- `emp_dept → dept_manager` where `emp_dept` isn't a superkey.

#### Solution for the above table:

**Departments:**

| emp_dept | dept_manager |
|----------|--------------|
| D001     | John Smith   |

**Assignments:**

| emp_id | emp_dept |
|--------|----------|
| 1001   | D001     |

---

## Transaction Properties

A transaction is an action, or a series of actions, carried out by a single user or an application program, which reads or updates the contents of a database.

A transaction has four key properties that are abbreviated **ACID**.

### ACID is a shortform name for:
- **Atomic**
- **Consistent**
- **Isolated**
- **Durability**

---

## ACID Properties

### 1. **Atomicity**
- Transactions are atomic – they don’t have parts (conceptually).
- Transactions can’t be executed partially; it should not be detectable that they interleave with another transaction.

### 2. **Consistency**
- Transactions take the database from one consistent state into another.
- In the middle of a transaction, the database might not be consistent.

### 3. **Isolation**
- The effects of a transaction are not visible to other transactions until it has completed.
- From outside, the transaction has either happened or not.
- This actually sounds like a consequence of **atomicity**.

### 4. **Durability**
- Once a transaction has completed, its changes are made permanent.
- Even if the system crashes, the effects of a transaction must remain in place.

---

## Example of a Transaction

### Transfer £50 from account A to account B

```text
Read(A)
A = A - 50
Write(A)
Read(B)
B = B + 50
Write(B)
