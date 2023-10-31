

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
- 