# Sample database
---
## Install sqlite

Windows users — open `powershell.exe`:-

```pwsh
mkdir $HOME\sqlite
cd $HOME\sqlite
curl -O sqlite.zip https://sqlite.org/snapshot/sqlite-tools-win32-x64-202310241106.zip
curl -O sampledata2.zip https://ribash.uk/sampledata2.zip
Expand-Archive -DestinationPath . .\sqlite.zip
Expand-Archive -DestinationPath . .\sampledata2.zip
```

Mac users — open `Terminal.app`:-

```zsh
mkdir $HOME/sqlite
cd $HOME/sqlite
curl -O https://ribash.uk/sampledata2.zip
unzip sampledata2.zip
```

---
## Download sample data

If the command line instructions don't work, do the following in the UI

- Download sample data from Teams
- save this into your sqlite directory
- unzip this 
- delete the zip archive
- install sqlite into the same directory

---
## Create a database

- open a new empty sample database

  - PC

```pwsh
.\sqlite3.exe .\sample.db
```

  - Mac

```zsh
sqlite3 sample.db
```

- note the default database is called “main”, not sample

---
## Create your schema

- create a schema

```sql
CREATE TABLE braccio_positions (
    entry_id INT NOT NULL,
    robot_name VARCHAR(255),
    time TIMESTAMP,
    motor_1 DECIMAL(10,2),
    motor_2 DECIMAL(10,2),
    motor_3 DECIMAL(10,2),
    motor_4 DECIMAL(10,2),
    motor_5 DECIMAL(10,2),
    motor_6 DECIMAL(10,2),
    electrical_power DECIMAL(10,2),
    PRIMARY KEY (entry_id, time)
);

CREATE TABLE braccio_robotic_arm (
    entry_id INT NOT NULL,
    robot_name VARCHAR(255),
    time TIMESTAMP,
    temperature DECIMAL(10,2),
    humidity DECIMAL(10,2),
    proximity DECIMAL(10,2),
    PRIMARY KEY (entry_id)
);
```


---
## What happened?

- read the schema

```sql
.schema
```
- slightly more comprehensive version from the `.dump` command
- we do not have a table listing (for instance) all the valid robot_names so we do not declare nor enforce a foreign key across the tables — so the default for foreign_keys=off is ok


---
## Load one record

- load data (this is just one example record)

```sql
INSERT INTO braccio_positions (
  entry_id,
  robot_name,
  time,
  motor_1,
  motor_2,
  motor_3,
  motor_4,
  motor_5,
  motor_6,
  electrical_power )
VALUES (
  9,
  'Braccio++ Robot 1_1',
  '2023-05-02T10:50:55+00:00',
  0,
  0,
  0,
  0,
  0,
  0,
  0
);
```
---
## What happened?

- did this work?

```sql
SELECT * FROM braccio_positions WHERE entry_id='9';
```

- Try and insert again — arrow up — it fails — why?
- Delete what is there

```sql
DELETE FROM braccio_positions;
```
---
## Bulk load data

- there is a .csv bulk load command for sqlite

```sql
.mode csv
.separator ","
.import --skip 1 Braccio_positions_1.csv braccio_positions
.import --skip 1 Braccio_positions_2.csv braccio_positions
.import --skip 1 Braccio_positions_3.csv braccio_positions
```
---
## What happened?

- did it work?

```sql
SELECT * FROM braccio_positions LIMIT 5;
SELECT DISTINCT electrical_power FROM braccio_positions LIMIT 10;
SELECT COUNT(*) FROM braccio_positions;
SELECT COUNT(*) FROM  braccio_positions WHERE electrical_power='90';
SELECT AVG(electrical_power) FROM braccio_positions;
```

---
## We have another table!

- CSV import again

```sql
.mode csv
.separator ","
.import --skip 1 Braccio_robotic_arm.csv braccio_robotic_arm

```

- again, we can query the data

```sql
SELECT * FROM braccio_robotic_arm LIMIT 5;
SELECT DISTINCT temperature FROM braccio_robotic_arm LIMIT 10;

```
---
## There are other valid approaches

There are many other valid approaches for the schema and the import

- one table for each of the csvs, entry ID is primary key
- autoincrement the entry ID, remove entry ID from CSV before entry
- clean up the csvs to remove extraneous trailling ','s
- create a table for robot_names and introduce referential integrity
- use NoSQL in the cloud

---
## Save the DB

- backup the db to disk

```sql
.output ./dump.sql
.dump 
.quit
```

- reload db

```pwsh
sqlite3 sample.db
```

