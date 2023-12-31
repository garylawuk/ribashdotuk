# Sample database
---
## Install sqlite

Windows users — open `powershell.exe`:-

```pwsh
mkdir $HOME\sqlite
cd $HOME\sqlite
curl -O sqlite.zip https://sqlite.org/snapshot/sqlite-tools-win32-x64-202310241106.zip
Expand-Archive .\sqlite.zip
```

Mac users — open `Terminal.app`:-

```zsh
mkdir $HOME/sqlite
cd !:1
```

---
## Download sample data

- Download sample data from [https://is.gd/sampledata_zip](https://is.gd/sampledata_zip)
- save this into your sqlite directory
- unzip this using `Expand-Archive` on Windows or `unzip` on Mac
- delete the zip archive

---
## Create a database

- open a new empty sample database

```zsh
sqlite3 sample.db
```

- note the default database is called “main”, not sample

---
## Create your schema

- create a schema
```sql
CREATE TABLE compressor_data (
  ID INT,
  Compressor VARCHAR(255),
  _time DATETIME,
  Air_Quantity INT,
  On_Load_Hours INT,
  Ready_Hours INT,
  Final_Temperature INT,
  Operating_Hours INT,
  Line_Pressure INT,
  Electric_Power FLOAT,
  PRIMARY KEY (ID, Compressor)
);
```

---
## What happened?

- read the schema

```sql
.schema
```
- slightly more comprehensive version from the `.dump` command

---
## Load one record

- load data (this is just one example record)

```sql
INSERT INTO compressor_data (
  ID,
  Compressor,
  _time,
  Air_Quantity,
  On_Load_Hours,
  Ready_Hours,
  Final_Temperature,
  Operating_Hours,
  Line_Pressure,
  Electric_Power)
VALUES (
  9,
  'Compressor_1',
  '08/12/2021 18:09',
  3184,
  7803,
  11227,
  887,
  10656,
  6946,
  263.5
);
```
---
## What happened?

- did this work?

```sql
SELECT * FROM Compressor_data WHERE ID='9' and Compressor='Compressor_1';
```

- Try and insert again — arrow up — it fails — why?
- Delete what is there

```sql
DELETE FROM Compressor_data;
```
---
## Bulk load data

- there is a .csv bulk load command for sqlite

```sql
.mode csv
.separator ","
.import --skip 1 Source_Data_Compressor_1.csv compressor_data
.import --skip 1 Source_Data_Compressor_2.csv compressor_data
.import --skip 1 Source_Data_Compressor_3.csv compressor_data
```
---
## What happened?

- did it work?

```sql
SELECT DISTINCT Compressor FROM compressor_data;
SELECT * FROM compressor_data LIMIT 5;
SELECT COUNT(*) FROM compressor_data;
SELECT COUNT(*) FROM compressor_data WHERE compressor='Compressor_3';
```
---
## Save the DB

- backup the db to disk

```sql
.output ./dump.sql
.dump Compressor_data
.quit
```

- reload db

```pwsh
sqlite3 sample.db
```

- real RDBMS do this for you, of course