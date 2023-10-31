

---

First a registered server needs to be added. Add this through the SQL Server 2022 that should be downloaded as well as SSMS. Make sure to tick the box that says 'Trust this source' or you will get an error.

# Part 1 - Importing data

![[Pasted image 20231031140734.png]]
*First step to importing data is creating a new database and calling it whatever you want (I have called mine 'CyclingSheffield') - then right click on the database you have created, and as seen below, click on import data. This will open the Import/Export data.*


![[Pasted image 20231031141737.png]] 
*Then select the data source to be Microsoft Access because of the file type and select the file.*

![[Pasted image 20231031142700.png]]
*Next choose the Destination as the Provider for SQL Server and just click next*

![[Pasted image 20231031142752.png]]
*Choose to copy the data*

![[Pasted image 20231031142851.png]]
*Select all tables you want to be copied from the file into your database*

![[Pasted image 20231031142928.png]]
*And finally leave it as run immediately and click finish to import your data*

Different ways of importing and exporting data:
- The above method is using the wizard to do it
- You can export to an XML file using a *SQL query*. The query would be:
```SQL
Select * from #tableName#
for xml raw
```

or for a json export
 
```SQL
Select * from tbl.branch #as an example
for json path, root ('branch')
```
