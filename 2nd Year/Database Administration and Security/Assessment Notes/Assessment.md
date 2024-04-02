
---

First a registered server needs to be added. Add this through the SQL Server 2022 that should be downloaded as well as SSMS. Make sure to tick the box that says 'Trust this source' or you will get an error.

# Task 1 - Importing data

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


# Task 2 - Automation#

**Automation**

Do a backup on the SQL Server Database to show automation working

![[Pasted image 20231031161929.png]]
*This is the first query that needs to be run so the first backup is made and the SQL Server Agent in the object explorer is created*

![[Pasted image 20231031162223.png]]
*Our Agent XPs was disabled to start off with so we had to run this command to be able to make a job*

![[Pasted image 20231031162340.png]]
*here is the backup in my temp folder in my C drive*

![[Pasted image 20231031162600.png]]
*Here is the general after right clicking on Jobs and pressing New Job, I created this general*

![[Pasted image 20231031162638.png]]
*This is the step created in the automation job. The command is the copied code from the creating the backup*

![[Pasted image 20231031162723.png]]
*This is the schedule of the backup job, its done every Monday at midnight *

Advantages of automation:7
- Increased level of productivity as the developers don't have to concern themselves with the little backups
- No human error so its much more reliable


# Task 3 - INSERT

![[Pasted image 20231102145354.png]]
*Shows the violation because of the duplicate of a primary key in the table*

![[Pasted image 20231102153358.png]]
![[Pasted image 20231102153449.png]]
*As you can see in the images above the insert into didn't work here because you cant insert data into the foreign key column when it doesn't exist in the primary key column*

![[Pasted image 20231102153853.png]]
*Delete doesn't work because it is the primary key. To delete a primary key you must delete all branches from that primary key*

# Task 4

![[Pasted image 20231102154737.png]]
*Procedure created that selects the total customers in the customer table*

![[Pasted image 20231102154824.png]]
*Here is the result of the procedure when executed*

# Task 5

![[Pasted image 20231120121339.png]]
*Code used to create a new table called `Audit_SOLine` *

![[Pasted image 20231120123003.png]]
*Create trigger to insert into the audit table whenever an update is made*

![[Pasted image 20231120123159.png]]
*Update query*

![[Pasted image 20231120123216.png]]
*Proof that it updates the audit table*


# Task 6


![[Pasted image 20231120130218.png]]
*Updating the netSale column to be quantity * selling price*

![[Pasted image 20231120134812.png]]
*This is the personSearch procedure*

![[Pasted image 20231120134843.png]]
*This is the result of executing the query*

# Task 7

![[Pasted image 20231120143227.png]]
*New users view of tblProduct*

![[Pasted image 20231120143332.png]]
*View that was created*

![[Pasted image 20231120143417.png]]
*Role called TeleSales_Staff that was made and the restrictions put on it for tblProduct*

![[Pasted image 20231120143507.png]]
*Login and user created for the server*

# Task 8
![[Pasted image 20231122131450.png]]
*Restore the back up media*

# Task 9
![[Pasted image 20231122135008.png]]
*Connected my ssms database to azure data studio - the cloud*

![[Pasted image 20231122135116.png]]
*Added 4th row of data to the region table in the cloud*

![[Pasted image 20231122135327.png]]
*It also edits it in the local table so its connected successfully*

![[Pasted image 20231122135533.png]]
*Deleted the row in ssms*

![[Pasted image 20231122135548.png]]
*It also has deleted from azure data studio so the connection goes both ways*

![[Pasted image 20231128140856.png]]
*New database in azure data studio and inserting data into new table called tblProduct*

![[Pasted image 20231128140939.png]]
*Also the azure database in the ssms*

![[Pasted image 20231128143148.png]]
*First five inserted in azure and they have been updated in ssms as well*

![[Pasted image 20231128150504.png]]
Inserted into ssms and it has updated on azure
# Task 10

# Task 11

![[Pasted image 20231207104925.png]]

![[Pasted image 20231207105731.png]]

![[Pasted image 20231207110327.png]]

![[Pasted image 20231207131758.png]]

![[Pasted image 20231207131807.png]]

