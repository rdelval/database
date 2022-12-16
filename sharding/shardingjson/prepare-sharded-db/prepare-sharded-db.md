# Prepare the Sharded DB

## Introduction

Now, we have 

*Estimated Lab Time:* 60 minutes.

Watch the video below for a quick walk through of the lab.
[Deployment the Sharded Database](videohub:1_m1amf8pg)

### Objectives

In this lab, you will connect to the sharded DB and create a sharded table to hold our JSON data.

### Prerequisites
This lab assumes you have:
- An Oracle Cloud account
- You have completed:
    - Lab: Prepare Setup (*Free-tier* and *Paid Tenants* only)
    - Lab: Environment Setup
    - Lab: Initialize Environment

## Task 1: Connect to the DB with SQL Developer

In the remote desktop session, open up SQL Developer by choosing **Applications** from the top menu, then select **Programming** and then select the **SQL Developer** icon.

First we need to create a DB connection to the Catalog DB. 


![Sqldevconnection](images/sqldevconnect.png)

1. Click the green Plus sign in the upper left of SQL Developer to add a new Connection.
2. A New Connection Window will appear as in the Screenshot above
3. Provide a name for the new connection such as 'Shard DB Catalog'
4. The Shard user is named 'app_schema' with a password of 'app_schema'
5. For the hostname use '10.0.20.150' This is the internal IP Address of the Catalog container. Port is 1521.
6. For the Service name use 'pcat1pdb'

A new connection will be created and we are now connected to the shard catalog as the user app_schema. From here we will proceed to use SQL Developer to create the necessary schemas.


## Task 2: Create the Sharded JSON table

While still connected to the Catalog DB in SQL Developer, goto the open file dialog by clicking on File->Open in SQL Developer. Then in Home, open up the folder 'sqlscripts' and select the file 'createshardtable.sql'

The script below should open up in SQL Developer:

```
/* Enable shard DDL */
ALTER SESSION ENABLE SHARD DDL;

/* Create a tablespace set */
CREATE TABLESPACE SET TSP_SET_1 USING TEMPLATE
 (datafile size 100m autoextend on next 10M maxsize unlimited
 extent management local segment space management auto);

/* Create the sharded table */
CREATE SHARDED TABLE CUSTOMERS
(
"ID" VARCHAR2(255) NOT NULL,
"CUSTPROFILE" JSON,
PRIMARY KEY (ID)
)
TABLESPACE SET TSP_SET_1
PARTITION BY CONSISTENT HASH (ID) PARTITIONS AUTO;
```
The script will create a tablespace set and then create a sharded table to hold our JSON. Aside from the JSON column itself, we have added another column to use as the shard key and set this as the Primary Key.

Run the script now in SQL Developer by choosing 'Run Script' - the second icon from the left in the SQL Develeoper tab. 

Expand the Results window at the bottom if necessary to see that the script executed successfully.





You may now proceed to the next lab.

## Acknowledgements
* **Author** - 
* **Contributors** - 

