# SQL Database on Azure

After creating the resource on the portal..


## Look around
Save some typing in future commands by configuring defaults
> az configure --defaults group=[sandbox resource group name] sql-server=[server-name]

> az configure --defaults group=holi sql-server=loogisticsdb

List your databases
> az sql db list

> az sql db list --output table

> az sql db list | jq '[.[] | {name: .name}]'

The last method will filter the json output

        [
            {
            "name": "Logistics"
            },
            {
            "name": "master"
            }
        ]

To show details about a specific database

> az sql db show --name Logistics

> az sql db show --name Logistics | jq '{name: .name, maxSizeBytes: .maxSizeBytes, status: .status}'

        {
        "name": "Logistics",
        "maxSizeBytes": 1073741824,
        "status": "Online"
        }


## Connect to the database

Get the connection string
> az sql db show-connection-string --client sqlcmd --name Logistics

Output:

    "sqlcmd -S tcp:loogisticsdb.database.windows.net,1433 -d Logistics -U <username> -P <password> -N -l 30"    

Replace with the correct username and password

> sqlcmd -S tcp:loogisticsdb.database.windows.net,1433 -d Logistics -U danib -P "123123abcABC" -N -l 30

sqlcmd is not a command in my computer, Will try with the cloud shell

It worked, but I also had to modify the firewall to allow the IP of the cloud shell to connect to the database.

## Use T-SQL (Transact-SQL)

Create a table
> CREATE TABLE Drivers (DriverID int, LastName varchar(255), FirstName varchar(255), OriginCity varchar(255));

Verify the table exists
> SELECT name FROM sys.tables;

> GO

Output:

        name
        ------------------------------
        Drivers

        (1 rows affected)

**It is very important to use the command 'GO', to apply the previuos command.**

Add a row (CREATE)
> INSERT INTO Drivers (DriverID, LastName, FirstName, OriginCity) VALUES (123, 'Zirne', 'Laura', 'Springfield');

>GO

Read the row (READ)

> SELECT DriverID, OriginCity FROM Drivers;

> GO

Modify the row (UPDATE)

> UPDATE Drivers SET OriginCity='Boston' WHERE DriverID=123;

>GO

Delete the row (DELETE)

> DELETE FROM Drivers WHERE DriverID=123;

> GO

Verify that it's empty 

> SELECT COUNT(*) FROM Drivers;

> GO