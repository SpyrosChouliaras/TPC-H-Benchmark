# TPC-H-Benchmark

Official TPC-H benchmark - http://www.tpc.org/tpch

# Setup

**SQLSERVER**

Install SQLSERVER

```bash
sudo apt-get update
sudo apt-get install mysqlclient20
sudo apt-get install mysql-server
```

(Optional)

To allow access open and add the my.cnf file:

```bash
sudo nano /etc/mysql/my.cnf

...
...
...
[mysqld]
skip-grant-tables
```

Restart MySQL

```bash
sudo systemctl restart mysql
```

**Linux**

Make sure the required development tools are installed:

Ubuntu:

```bash
sudo apt-get update
sudo apt-get install gcc
```
Run the following command to see **gcc** version

```bash
gcc --version
```
Then run the following commands to clone the repo and build the tools:

```bash
git clone https://github.com/SpyrosChouliaras/TPC-H-Benchmark.git
```

Also install **make**
```bash
sudo apt install make
```


Move to the dbgen directory

```bash
cd TPC-H-Benchmark/dbgen
```

create the makefile

```bash
 cp makefile.suite makefile
 ```
 
 Add the file
 
 ```bash
 sudo nano makefile
 ```
 
 In dbgen folder find the created makefile and insert the values to this file.

```bash
################
## CHANGE NAME OF ANSI COMPILER HERE
################
CC      = gcc  # Insert the Compiler type
# Current values for DATABASE are: INFORMIX, DB2, TDAT (Teradata)
#                                  SQLSERVER, SYBASE, ORACLE, VECTORWISE
# Current values for MACHINE are:  ATT, DOS, HP, IBM, ICL, MVS, 
#                                  SGI, SUN, U2200, VMS, LINUX, WIN32 
# Current values for WORKLOAD are:  TPCH
DATABASE= SQLSERVER #DB Type
MACHINE = LINUX #OS
WORKLOAD = TPCH #Benchmark
#
...
```
Save and exit

Run the command make

```bash
make
```

# Environment
Set these env va
riables correctly:

```bash

sudo nano ~/.bashrc 

...
...
...
...
#Add these lines including your dbgen path

export DSS_CONFIG=/.../tpch-kit/dbgen
export DSS_QUERY=$DSS_CONFIG/queries
export DSS_PATH=/path-to-dir-for-output-files
```
Exit and source the file

```bash
source ~/.bashrc
```

# Data generation
Generate the files for population. (The last numeric parametr determines the volume of data with which will be your database then populated - I decided that 0.1 (=100MB) is fine for my purposes, since I am not interested in the database benchmark tests.

```bash 
./dbgen -s 0.1
```
# SQLSERVER connection

Connect to SQL server with permission to reach local files, create database and connect to schema.

```bash
$ mysql -u root -p --local-infile
$ mysql> CREATE DATABASE tpch;
$ mysql> USE tpch;
```

Run following queries in SQL console uploaded in this repository.

```bash
CREATE TABLE NATION  ( N_NATIONKEY  INTEGER NOT NULL,
                            N_NAME       CHAR(25) NOT NULL,
                            N_REGIONKEY  INTEGER NOT NULL,
                            N_COMMENT    VARCHAR(152));

CREATE TABLE REGION  ( R_REGIONKEY  INTEGER NOT NULL,
                            R_NAME       CHAR(25) NOT NULL,
                            R_COMMENT    VARCHAR(152));

CREATE TABLE PART  ( P_PARTKEY     INTEGER NOT NULL,
                          P_NAME        VARCHAR(55) NOT NULL,
                          P_MFGR        CHAR(25) NOT NULL,
                          P_BRAND       CHAR(10) NOT NULL,
                          P_TYPE        VARCHAR(25) NOT NULL,
                          P_SIZE        INTEGER NOT NULL,
                          P_CONTAINER   CHAR(10) NOT NULL,
                          P_RETAILPRICE DECIMAL(15,2) NOT NULL,
                          P_COMMENT     VARCHAR(23) NOT NULL );

CREATE TABLE SUPPLIER ( S_SUPPKEY     INTEGER NOT NULL,
                             S_NAME        CHAR(25) NOT NULL,
                             S_ADDRESS     VARCHAR(40) NOT NULL,
                             S_NATIONKEY   INTEGER NOT NULL,
                             S_PHONE       CHAR(15) NOT NULL,
                             S_ACCTBAL     DECIMAL(15,2) NOT NULL,
                             S_COMMENT     VARCHAR(101) NOT NULL);

CREATE TABLE PARTSUPP ( PS_PARTKEY     INTEGER NOT NULL,
                             PS_SUPPKEY     INTEGER NOT NULL,
                             PS_AVAILQTY    INTEGER NOT NULL,
                             PS_SUPPLYCOST  DECIMAL(15,2)  NOT NULL,
                             PS_COMMENT     VARCHAR(199) NOT NULL );

CREATE TABLE CUSTOMER ( C_CUSTKEY     INTEGER NOT NULL,
                             C_NAME        VARCHAR(25) NOT NULL,
                             C_ADDRESS     VARCHAR(40) NOT NULL,
                             C_NATIONKEY   INTEGER NOT NULL,
                             C_PHONE       CHAR(15) NOT NULL,
                             C_ACCTBAL     DECIMAL(15,2)   NOT NULL,
                             C_MKTSEGMENT  CHAR(10) NOT NULL,
                             C_COMMENT     VARCHAR(117) NOT NULL);

CREATE TABLE ORDERS  ( O_ORDERKEY       INTEGER NOT NULL,
                           O_CUSTKEY        INTEGER NOT NULL,
                           O_ORDERSTATUS    CHAR(1) NOT NULL,
                           O_TOTALPRICE     DECIMAL(15,2) NOT NULL,
                           O_ORDERDATE      DATE NOT NULL,
                           O_ORDERPRIORITY  CHAR(15) NOT NULL,  
                           O_CLERK          CHAR(15) NOT NULL, 
                           O_SHIPPRIORITY   INTEGER NOT NULL,
                           O_COMMENT        VARCHAR(79) NOT NULL);

CREATE TABLE LINEITEM ( L_ORDERKEY    INTEGER NOT NULL,
                             L_PARTKEY     INTEGER NOT NULL,
                             L_SUPPKEY     INTEGER NOT NULL,
                             L_LINENUMBER  INTEGER NOT NULL,
                             L_QUANTITY    DECIMAL(15,2) NOT NULL,
                             L_EXTENDEDPRICE  DECIMAL(15,2) NOT NULL,
                             L_DISCOUNT    DECIMAL(15,2) NOT NULL,
                             L_TAX         DECIMAL(15,2) NOT NULL,
                             L_RETURNFLAG  CHAR(1) NOT NULL,
                             L_LINESTATUS  CHAR(1) NOT NULL,
                             L_SHIPDATE    DATE NOT NULL,
                             L_COMMITDATE  DATE NOT NULL,
                             L_RECEIPTDATE DATE NOT NULL,
                             L_SHIPINSTRUCT CHAR(25) NOT NULL,
                             L_SHIPMODE     CHAR(10) NOT NULL,
                             L_COMMENT      VARCHAR(44) NOT NULL);
```

Populate tables with generated dummy data.

```bash
LOAD DATA LOCAL INFILE 'customer.tbl' INTO TABLE CUSTOMER FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'orders.tbl' INTO TABLE ORDERS FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'lineitem.tbl' INTO TABLE LINEITEM FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'nation.tbl' INTO TABLE NATION FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'partsupp.tbl' INTO TABLE PARTSUPP FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'part.tbl' INTO TABLE PART FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'region.tbl' INTO TABLE REGION FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'supplier.tbl' INTO TABLE SUPPLIER FIELDS TERMINATED BY '|';
```


Check that the data have been loaded correctly to the database by running a simple query:

```basH
$mysql> DESCRIBE CUSTOMER;
$mysql> select C_ACCTBAL from CUSTOMER where C_ACCTBAL between 1900 and 2000;
```

Since you loaded the data and changed the sql queries based on SQLSERVER (see SQLSERVER queries file) then you can run a query from MySQL.

```bash
$ mysql> mysql -u root -p --local-infile
$ mysql> use tpch;
$ mysql> source /full-path-to-queries/1.sql #example to run query 1 (Q1)
