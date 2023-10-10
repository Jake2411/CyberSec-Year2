
>[!info]- Migration
>DBA's often transfer data to and from text files, Excel spreadsheets, Access, Oracle or other SQL Server databases
>This process is called migration
>Can be performed through a variety of methods
>This is a major piece of work and one of the major tasks of the DBA

Importing data is when an external source is loaded into a new database. Often the new databases is on a different database platform with different database structures

Exporting data is one database system is moved out to another application. The receiving system need not be a structured database e.g.:
- A spreadsheet
- table in a word document
- HTML table

**Reasons for moving data:**
- Archive
- Migration
- Upgrade
- Data Warehouse
- Web Application ([[Week 1 |MVC]])
- Testing
- Development
- Performance Analysis
- Reporting
- Legal

>[!info]- Archiving Data
>- Too much data in an operational database system causes the system to become inefficient.
>- Usually only about 2 to 3 years worth of transactions will exist in such a system.
>- Data is archived from such a system on a regular basis (at every month for example)

**Methods of moving data:**
- Text files
- Open Databases Connectivity (ODBC)
- Java Database Connectivity (JDBC)
- Bulk Copy Program (BCP)
- Data Transformation Services (DTS)
- Backup/restore
- Log shipping
- Replication/mirror

>[!definition]- CSV file
>CSV File is a Comma Separated File, each column is separated by a comma

>[!definition]- Text File (Fixed Width)
>Theses are files where the individual columns are separated by a fixed number of characters. These are clearer to read but can be much larger in size because each field will be padded with spaces to make up the column width

>[!definition]- ODBC
>Open Database Connectivity is a file protocol to allow data to be read in a different database system (reading oracle in a SQL-Server)
>It can be slow where large amounts of data are transferred

>[!definition]- Data Transformation Services
>DTS is a facility within SQL-Server that enables:
>- Import and export between any data source and destination
>- Transform any data
>- Transfer databases objects between databases in SQL-Server

DTS Designer allows experienced Database Administrators to:
- import and export data
- transform data
- define complex workflows for heterogeneous data from multiple sources#

DTS Designer allows easy transfer of database objects between SQL server Databases.
Objects to be transferred can include tables, views, stored procedures, logins, users, roles and constraints

SQL Server provides a number of other options for importing and exporting data:
- Generate a script
- Publish Database
- Deploy to Azure SQL Database
- Deploy to Azure VM
- Deploy to Polybase

>[!info]- Bulk Copy Program
>- BCP is a command prompt utility that imports or exports database data or text files
>- Performed from O/S command prompt
>- Allow import or export of data to/from a text file or a binary file
>- The data migrated can be a table or a SQL query result
>- BCP can bypass transaction log files, which improves speed but can compromise recovery
>- Always backup database before and after BCP operation

```SQL
USE pubs
GO

TRUNCATE TABLE authors
GO

BULK INSERT authors from "c\temp\authors.csv"
GO

SELECT * FROM authors
GO
```

>[!info]- SELECT INTO
>- SELECT INTO statement copies data from one table to another
>- Tables can be on the same SQL server or linked SQL serv