

>[!Definition]- Database Definition
>*A database is an organized collection of structured information, or data, typically stored electronically in a computer system. A database is usually controlled by a Database Management System (DBMS).*

A DBMS:
	1. Enables DB access and control
	2. Interface between programs and data

>[!Definition]- DBA Definition
>*A Database Administrator is the information technician responsible for ensuring the ongoing operational functionality and efficiency of an organization's databases and the applications that access those databases.*

Some of a DBA's responsibilities:
- Security
	- Auditing
	- Permissions
	- Encryption
- Availability
	- Redundancy
	- Monitoring
	- Log Shipping
- Reliability
	- Performance Tuning
	- Load Testing
- Recoverability
	- Backups
	- Restore Verification
	- Documentation

Some example tasks of the DBA:
- Import and export data in a variety of formats
- Link to other databases
- Produce reports
- Create automated tasks
- Backup/restore
- Performance
- Manage users/security
- Manage resources

---

>[!info]- Three Tier Architecture
>Client: HTML, JavaScript, CSS
>Application Server: Python, .NET, PHP, Java, Node
>Database: SQL 

**Advantages of 3 tier architecture:

- Tiers can be independently modified, maintained or replaced.
- Thin Clients: Only presentation layer at the clients device (the html ...)
- Integrated Data Access: Several database systems can be handled transparently at the middle layer
- Central management of connections and code
- Scalability
- Interaction between tiers through well defined API's

----

**MVC Model**

![MVC model | 400x300](https://developer.mozilla.org/en-US/docs/Glossary/MVC/model-view-controller-light-blue.png)

---

>[!info]- RBDMS
>Relational Database Management System (RBDMS), relational here means that the SQL can create connections between pieces of data.
>These connections are called keys
>Connections between tables are based on the primary key and the secondary key

**Components of:**
	**SQL Server Configuration Manager:**
		- Start, stop and restart SQL server services 
		- Change service account used to start a service
		- Configure network protocols
		- Change IP addresses and TCP ports used by SQL server
	**SQL Server Management Studio:**
		- DBA primary interface
		- Configure and interact with databases from a single console

>[!definition]- Table
>A table is a collection of related data entries in rows and columns

>[!definition]- Field
>A field is a column in a database table

---

**RBDMS Data Integrity:**

Entity Integrity - There are no duplicate rows in a table

Domain Integrity - Enforces valid entries for a given field by restricting the type, format, or range of values

Referential Integrity - Rows cannot be deleted, which are used by other records

User-defined Integrity - Enforces some specific business rules that do not fall into entity, domain or referential integrity

---

>[!info]- SQL Server Databases
>SQL Server Databases have 2 kinds of databases:
>- System databases (already on the DBMS)
>- User databases (put into the DBMS by the user)

**System Databases:**

**Master:** Controls user DB's and SQL server operations, tracks user accounts and system error messages

**Model:** Provides templates for new databases

**Tempdb:** Storage area for temporary tables and other temporary working storage needs

**MSDB:** Storage area for scheduling information and job history

---

>[!info]- SQL Server non relational data
>SQL Server also includes support for structured and semi-structured data, including digital media formats for:
>- text
>- pictures
>- audio
>- video
>- other multimedia data

>[!definition]- Indexes
>Indexes in databases are data structures that are used to improve the speed of data retrieval operations on a database table. They work in a similar way to the index in a book, providing a quick reference to where specific data is located within the table. Indexes are an essential component of database management systems (DBMS) and play a crucial role in optimizing query performance.

Non clustered indexes - don't change the sort order of the data in the database

Clustered indexes - Change the sort order

Database Files:
- .mdf primary data files
- .ndf secondary data files
- .ldf transaction log files

>[!info]- Transparent Data Encryption
>Transparent Data Encryption (TDE) allows the encryption of databases and backups with no user impact

Auditing allows the tracking of data access and modification. 