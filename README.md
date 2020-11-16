# TPC-H-Benchmark

Official TPC-H benchmark - http://www.tpc.org/tpch

# Setup

**Linux**

Make sure the required development tools are installed:

Ubuntu:

sudo apt-get install git make gcc
CentOS/RHEL:

sudo yum install git make gcc

Then run the following commands to clone the repo and build the tools:

```bash
git clone https://github.com/SpyrosChouliaras/TPC-H-Benchmark.git
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
 
 In dbgen folder find the created makefile and insert highlighted values (bold) to this file.

```bash
################
## CHANGE NAME OF ANSI COMPILER HERE
################
CC      = gcc
# Current values for DATABASE are: INFORMIX, DB2, TDAT (Teradata)
#                                  SQLSERVER, SYBASE, ORACLE, VECTORWISE
# Current values for MACHINE are:  ATT, DOS, HP, IBM, ICL, MVS, 
#                                  SGI, SUN, U2200, VMS, LINUX, WIN32 
# Current values for WORKLOAD are:  TPCH
DATABASE= "QLSERVER"
MACHINE = *LINUX*
WORKLOAD = *TPCH*
#
...
```
