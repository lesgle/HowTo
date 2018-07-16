# SQL Server 2016 - Always On Availability Groups

In this blog post I will provide basic step by step guide how to install and configure SQL Server 2016 with Always On Availability Groups. Migration WSUS from Windows Internal Database to Availability Group will be as example.

This guide has the following steps:

[1. Preparation](#prepare)  
[2. Installing SQL Server 2016 on both SQL servers](#install)
3. Installing Failover Clustering Feature on SQL servers and enable AlwaysOn  
4. Working with WSUS  
5. Create Availability Group  
6. Create logins and grant permissions  
7. Create Availability Group Listener  
8. Finish WSUS server reconfiguration

## <a name="prepare"></a> Preparation

Two Windows Servers 2012 R2 - SQL01 and SQL02.

Each SQL server has the following drive mapping:  
C - System  
D - SQL Installation  
E - Database volume  
F - SQL Logs  
G - Backups

WSUS server is Windows Server 2012 R2 with default configuration.

Dedicated Active Directory Organizational Unit for SQL servers computer objects.

Opened firewall TCP ports on both SQL servers - 1433, 1434, 5022.

## <a name="install"></a> Installing SQL Server 2016 on both SQL servers

From SQL Server Installation Center click Installation then "New SQL Server stand-alone installation or add features to an existing installation".

![1](jpg/1.jpg)