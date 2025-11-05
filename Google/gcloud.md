# Setup Google Cloud Server
1. Create an Microsoft SQL Server "xpert-server" on Google Cloud.
2. Install GCloud CLI (https://cloud.google.com/sdk/docs/install).
3. Run "gcloud auth login" to authenticate. 
4. Install .NET by running "sudo apt-get install -y dotnet-sdk-8.0"
5. Install sqlpackage by running "dotnet tool install -g microsoft.sqlpackage"
6. Run "export PATH="$PATH:/home/nassoskranidiotis/.dotnet/tools""
7. On SQL/Connections/Networking/Authorized Networks, allow 0.0.0.0/0
8. On SQL/Connection/Security, allow unencrypted network traffic.
9. Run the following command to import a bacpac file and create a new database:

```
sqlpackage /Action:Import /tsn:server_IP_here,1433 /tdn:db_name /tu:username_here /sf:db.bacpac /TargetTrustServerCertificate:True /tp:"your_password_here"
```

10. To export a bacpac, run:

```
sqlpackage /Action:Export /SourceServerName:server_IP_here,1433 /SourceDatabaseName:db_name /SourceUser:username_here /SourcePassword:"your_password_here" /TargetFile:db1.bacpac /SourceTrustServerCertificate:True
```