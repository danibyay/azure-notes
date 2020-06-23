
>az configure --defaults group=migration

Create postgre db server in azure portal. Choose the cheapest option.

### Install psql (postgre client) for mac with homebrew.

Tutorial [here](https://blog.timescale.com/tutorials/how-to-install-psql-on-mac-ubuntu-debian-windows/)

https://docs.microsoft.com/en-us/azure/postgresql/quickstart-create-server-database-portal

When you create your Azure Database for PostgreSQL server, a default database named postgres is created.

But I want a techconfdb.. How? 


### Connect to server

psql --host=postgreserver-nd.postgres.database.azure.com --port=5432 --username=hello@postgreserver-nd --dbname=postgres

### Migrate data using the script

> psql --host=postgreserver-nd.postgres.database.azure.com --port=5432 --username=hello@postgreserver-nd --dbname=postgres -i \<path_to/restore.sql>

This will create the techconfdb database and use it.


Service bus name : busndmigrate

Notification queue name: myndqueue

DB Server : postgreserver-nd

Storage account name: migrationstoragend

App service name: migration2service

Connection string for service is located in the key icon "shared access policies".


## afsadf

only the first time. Create a virtual env with python 3
> python3 -m venv venv


