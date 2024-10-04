# psql

run psql in the DB host or inside the container 

we might need to supply the admin username to get root/admin privilage.

```sh
psql -U postgres

```
## basic command line

```sh
psql

```
## exit from psql shell

```
\q
```

## connect to DB with superuser postgres from CLI
```sh
sudo -i -u postgres psql
```

## clear screen 
```
\! clear
```

## connect to local DB via CLI
```
psql -d <dbname> -U <username>
```

## connect to a remote DB via CLI
```
psql -h <hostname> -d <dbname> -U <username>
psql -h localhost -p 5432 -U postgres
```

## create user in iteractive mode (from terminal>
```
createuser --interactive --pwprompt <username>
```

## create user from cli (terminal) with specify url, port, user
```
createuser -h localhost -p 5432 -U postgres -P -s -e <username>
```

## list users
```sh
\du
\du <username>
SELECT usename FROM pg_user;

```

## reset password
```sh
\password <user>
```

## list all database
```sh
\l
```

## create DB
```sh
CREATE DATABASE <db-name>;  
```

## swtich to DB
```
\c <db-name>
```

## Create a table
```
CREATE TABLE table_name(fields definition);

# without columns
CREATE TABLE test()
```

## Rename a table
```
ALTER TABLE table_name RENAME TO new_table_name;
```

## list DB tables
```
\dt
```

## describe table
```
\d <table-name>
```

## list all DB functions
```
\df
```

## list all views
```
\dv
```

## run commands from file
```
\i <psql-filename>
```

## ALTER table (add a foreign key)
```
ALTER TABLE books ADD COLUMN author_id INT REFERENCES authors(author_id);
```

## Insert data
```
INSERT INTO authors (first_name, last_name) 
VALUES ('Tamsyn', 'Muir'), ('Ann', 'Leckie'), ('Zen', 'Cho');

INSERT INTO books(title, published_year, author_id) 
VALUES ('Gideon the Ninth', 2019, 1), ('Ancillary Justice', 2013, 2), ('Black Water Sister', 2021, 3);
```

## timing (measure)
```
\timing
operations ...
\timing
```

## import data from a CSV file with copy command
from this DB table
```
CREATE DATABASE films_db;

\c films_db

CREATE TABLE films(
	id SERIAL PRIMARY KEY,
	title VARCHAR(100),
	year INT,
	running_time INT
);
```
The CSV file contains columns Title, Year, Running Time
then the copy command will be
```
\copy films(title, year, running_time) FROM 'path_to_file' DELIMITER ‘,’ CSV HEADER;
```

## Backup a database table with pg_dump command
```sh
pg_dump -U admin dummy > dummy_db.sql
```

## Restore a database with psql (.sql)
```sh
psql -U admin -d dummy_copy -f dummy_db.sql
```
## Restore a database with pg_restore (.tar)
```sh
pg_restore -U admin -d dummy_copy -dummy_db.tar
```
