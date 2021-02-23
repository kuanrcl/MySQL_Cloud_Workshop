# Loading TCPH Data into HeatWave

## Generate TPCH Data
We will be using the Oracle HeatWave github page as reference (https://github.com/oracle/heatwave-tpch)

* Download the TPCH toolkit from http://www.tpc.org/tpc_documents_current_versions/download_programs/tools-download-request5.asp?bm_type=TPC-H&bm_vers=2.18.0&mode=CURRENT-ONLY
* Unzip the toolkit 

```
cd dbgen
cp makefile.suite makefile
```

* Edit the makefile with the following parameters/values
```
CC=gcc
DATABASE=ORACLE
MACHINE=LINUX
WORKLOAD=TPCH
```

* Compile and generate TPCH data

```
make
./dbgen -s 1
ls -al *.tbl
```
## Create TPCH Database

* Grab the create_tables.sql from https://github.com/oracle/heatwave-tpch/blob/main/TPCH/create_tables.sql

```
mysql -uadmin -h<load_balancer> -P3306 -p<password> < create_tables.sql
```

## Load the data

```
mysqlsh
\c admin@<load_balancer>:3306
util.importTable("./customer.tbl", {schema:"tpch_1024", table:"CUSTOMER",dialect: "csv-unix", fieldsTerminatedBy: "|", showProgress: true})
util.importTable("./region.tbl", {schema:"tpch_1024", table:"REGION",dialect: "csv-unix", fieldsTerminatedBy: "|", showProgress: true, threads: 8})
util.importTable("./nation.tbl", {schema:"tpch_1024", table:"NATION",dialect: "csv-unix", fieldsTerminatedBy: "|", showProgress: true, threads: 8})
util.importTable("./part.tbl", {schema:"tpch_1024", table:"PART",dialect: "csv-unix", fieldsTerminatedBy: "|", showProgress: true, threads: 8})
util.importTable("./supplier.tbl", {schema:"tpch_1024", table:"SUPPLIER",dialect: "csv-unix", fieldsTerminatedBy: "|", showProgress: true, threads: 8})
util.importTable("./partsupp.tbl", {schema:"tpch_1024", table:"PARTSUPP",dialect: "csv-unix", fieldsTerminatedBy: "|", showProgress: true, threads: 8})
util.importTable("./orders.tbl", {schema:"tpch_1024", table:"ORDERS",dialect: "csv-unix", fieldsTerminatedBy: "|", showProgress: true, threads: 8})
util.importTable("./lineitem.tbl", {schema:"tpch_1024", table:"LINEITEM",dialect: "csv-unix", fieldsTerminatedBy: "|", showProgress: true, threads: 8}) 
\sql
SELECT table_schema AS "Database",  ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)"  FROM information_schema.TABLES  GROUP BY table_schema;
\q
```

##  Load the data into HeatWave in-memory clusters

Run the secondary_load.sql using https://github.com/oracle/heatwave-tpch/blob/main/HeatWave/secondary_load.sql

## Test the HeatWave engine

You are now ready to test the power of HeatWave by running any of the TPCH queries (https://github.com/oracle/heatwave-tpch/tree/main/TPCH)
Run the queries against MDS, then load the data into HeatWave in-memory cluster, and re-run the queries again



