# Setup a MS SQL Server locally with Docker

Create an installation folder: 

```
mkdir sql-docker
cd sql-docker
```

Create a docker-compose.yml file with contents:

```
services:
  sqlserver:
    image: mcr.microsoft.com/mssql/server:latest
    container_name: sqlserver
    restart: always
    environment:
      ACCEPT_EULA: 'Y'
      SA_PASSWORD: your_password
    ports:
      - '1433:1433'
```

The server’s credentials are:

```
host: localhost
username: sa
password: your_password
port: 1433
```

Create Docker container `docker compose up -d`.

Copy the .bak file: Use the docker cp command to copy the file from your host machine into the running container:

```
docker cp /path/to/your/backup.bak <container_name_or_id>:/var/opt/mssql/
```

# Restore database from bak file

Find logical names: Connect to your SQL Server instance (e.g., using DBeaver) and run the RESTORE DATABASE T-SQL command, specifying the path to the file inside the container:

```
RESTORE FILELISTONLY
FROM DISK = N'/var/opt/mssql/backup.bak';
GO
```

Execute the Restore Command: Connect to your SQL Server instance (e.g., using DBeaver) and run the RESTORE DATABASE T-SQL command, specifying the path to the file inside the container:

```
RESTORE DATABASE YourNewDatabaseName
FROM DISK = N'/var/opt/mssql/backup.bak'
WITH FILE = 1,
MOVE N'LogicalDataFileName' TO N'/var/opt/mssql/data/YourNewDatabaseName.mdf',
MOVE N'LogicalLogFileName' TO N'/var/opt/mssql/data/YourNewDatabaseName_log.ldf',
NOUNLOAD, REPLACE, STATS = 5;
```

# Back up database

```
BACKUP DATABASE YourNewDatabaseName 
TO DISK = '/var/opt/mssql/backup.bak' 
WITH COMPRESSION, STATS = 10;
```

Copy the bak file from inside the container to your local folder:

```
docker cp sqlserver_container:/var/opt/mssql/backup/backup.bak /home/nassoskranidiotis/
```
