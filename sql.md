# Learn the basic

## What Are Relational Databases?

- A relational database is a collection of data points with pre-defined relationships between them.
- The relational model organizes data into tables — with each row representing an individual record and each column consisting of attributes that contain values. This tabular database structure makes it easy to establish relationships between data points so that the information can be accessed in a variety of different ways without reorganizing the data itself.
- Key features include:
  - Use of SQL (Structured Query Language) for querying and managing data
  - Support for ACID transactions (Atomicity, Consistency, Isolation, Durability)
  - Enforcement of data integrity through constraints (e.g., primary keys, foreign keys)
  - Ability to establish relationships between tables, enabling complex queries and data retrieval
  - Scalability and support for multi-user environments

## RDBMS Benefits and Limitations

- Benefits
  - Structured Data: RDBMS allows data storage in a structured way, using rows and columns in tables -> ensuring efficient and flexible usage.
  - ACID Properties: ACID stands for Atomicity, Consistency, Isolation, and Durability. These properties ensure reliable and safe data manipulation in a RDBMS, making it suitable for mission-critical applications
  - Normalization: RDBMS supports data normalization, a process that organizes data in a way that reduces data redundancy and improves data integrity.
  - Scalability: RDBMSs generally provide good scalability options, allowing for the addition of more storage or computational resources as the data and workload grow.
  - Data Integrity: RDBMS provides mechanisms like constraints, primary keys, and foreign keys to enforce data integrity and consistency, ensuring that the data is accurate and reliable.
  - Security: RDBMSs offer various security features such as user authentication, access control, and data encryption to protect sensitive data.
- Limitations
  - Complexity: Setting up and managing an RDBMS can be complex, especially for large applications.
  - Cost: RDBMSs can be expensive, both in terms of licensing fees and the computational and storage resources they require.
  - Fixed Schema: RDBMS follows a rigid schema for data organization, which means any changes to the schema can be time-consuming and complicated.
  - Handling of Unstructured Data: RDBMSs are not suitable for handling unstructured data like multimedia files, social media posts, and sensor data, as their relational structure is optimized for structured data
  - Horizontal Scalability: RDBMSs are not as easily horizontally scalable as NoSQL databases. Scaling horizontally, which involves adding more machines to the system, can be challenging in terms of cost and complexity.

# Basic SQL Syntax

# Data Definition Language (DDL)

# Data Manipulation Language (DML)

# Aggregate Queries

# Data Constraints

# SQL JOIN Queries

# Sub Queries

## Nested Subqueries

## Correlated Subqueries

## Different type

### Scalar