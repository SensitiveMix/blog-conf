---
title: 你一定遇到 SqlServer 安装依赖的坑之Microsoft SqlServer Management SDK SFC
date: 2017-06-11 22:33:02
categories:
  - 数据库
tags: 
  - SqlServer
  - Database
---

I have installed MS SQL Server 2012 R2 and when I try to update model from database under EDMX file I am facing that error.



Could not load file or assembly Microsoft.SqlServer.management.sdk.sfc version 11.0.0.0



Solutions:



IF Microsoft.SqlServer.management.sdk.sfc version 12.0.0.0 cannot find by visual studio
Goto:

http://www.microsoft.com/en-us/download/details.aspx?id=42295



DownLoad:

ENU\x86\SharedManagementObjects.msi for X64 OS  OR
ENU\x64\SharedManagementObjects.msi for X64 OS (According  to our computer)


IF Microsoft.SqlServer.management.sdk.sfc version 11.0.0.0 cannot find by visual studio
Goto:

https://www.microsoft.com/en-us/download/details.aspx?id=35580



DownLoad:

ENU\x86\SharedManagementObjects.msi for X64 OS  OR
ENU\x64\SharedManagementObjects.msi for X64 OS (According  to our computer)


IF Microsoft.SqlServer.management.sdk.sfc version 10.0.0.0 cannot find by visual studio
Goto:

https://www.microsoft.com/en-us/download/details.aspx?id=26728



DownLoad:

ENU\x86\SharedManagementObjects.msi for X64 OS  OR
ENU\x64\SharedManagementObjects.msi for X64 OS (According  to our computer)




After intalled,and restart visual studio, you will find everything work normally.
