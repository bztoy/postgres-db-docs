# Postgres with Docker

(the official document on Github)[https://github.com/docker-library/docs/blob/master/postgres/README.md]

### Docker volumes for PostgreSQL


### PGDATA
default /var/lib/postgresql/data

```sh
docker run -it --rm --name postgres `
  -e POSTGRES_PASSWORD=admin123 `
  -v ${PWD}/pgdata:/var/lib/postgresql/data `
  -p 5432:5432 `
  postgres:17.0

```

basic docker compose file example (docker-compose.yaml)

version: '3.1'
services:
  db:
    image: postgres:15.0
    restart: always
    environment:
      POSTGRES_PASSWORD: admin123
    ports:
    - 5000:5432
    volumes:
    - ./pgdata:/var/lib/postgresql/data
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080

## Environment Variable
POSTGRES_USER: Username for the Postgres Admin
POSTGRES_PASSWORD: Password for the Postgres Admin
POSTGRES_DB: Default database for your Postgres Server
PGDATA: Path where data is stored

export PGDATA as below is very interesting
-e PGDATA=/var/lib/postgresql/data/pgdata \


```sh
$ docker run -d \
	--name some-postgres \
	-e POSTGRES_PASSWORD=mysecretpassword \
	-e PGDATA=/var/lib/postgresql/data/pgdata \
	-v /custom/mount:/var/lib/postgresql/data \
	postgres
```

## the configuration file
### postgresql.conf
data_directory

data_directory = '/data'
hba_file = '/config/pg_hba.conf'
ident_file = '/config/pg_ident.conf'

#### more interesting config
port = 5432
listen_addresses = '*'
max_connections = 100
shared_buffers = 128MB
dynamic_shared_memory_type = posix
max_wal_size = 1GB
min_wal_size = 80MB
log_timezone = 'Etc/UTC'
datestyle = 'iso, mdy'
timezone = 'Etc/UTC'

#locale settings
lc_messages = 'en_US.utf8'			# locale for system error message
lc_monetary = 'en_US.utf8'			# locale for monetary formatting
lc_numeric = 'en_US.utf8'			# locale for number formatting
lc_time = 'en_US.utf8'				# locale for time formatting

default_text_search_config = 'pg_catalog.english'

wal stands for Write Ahead Log

### pg_hba.conf File
local access withint the container does not require the password come from the following config
# "local" is for Unix domain socket connections only
local   all             all                                     trust

### pg_ident.conf
This config file is a mapping file between system users and database users.

### Specifying Custom Configuration
If we run on Linux, we need to ensure that the postgres user which has a user ID of 999 by default, should have access to the configuration files.

sudo chown 999:999 config/postgresql.conf
sudo chown 999:999 config/pg_hba.conf
sudo chown 999:999 config/pg_ident.conf

### running postgres with the custom configs
```sh
docker run -it --rm --name postgres `
-e POSTGRES_USER=postgresadmin `
-e POSTGRES_PASSWORD=admin123 `
-e POSTGRES_DB=postgresdb `
-e PGDATA="/data" `
-v ${PWD}/pgdata:/data `
-v ${PWD}/config:/config `
-p 5000:5432 `
postgres:15.0 -c 'config_file=/config/postgresql.conf'

```
the most important option is -c 'config_file=/config/postgresql.conf' 

### Enable Write-Ahead Log and Replication
wal_level = replica
max_wal_senders = 3

Enable Archive
archive_mode = on
archive_command = 'test ! -f /mnt/server/archive/%f && cp %p /mnt/server/archive/%f'

### Failover
```sh
docker exec -it postgres-2 bash

# confirm we cannot create a table as its a stand-by server
CREATE TABLE customers (firstname text, customer_id serial, date_created timestamp);

# run pg_ctl as postgres user (cannot be run as root!)
runuser -u postgres -- pg_ctl promote

# confirm we can create a table as its a primary server
CREATE TABLE customers (firstname text, customer_id serial, date_created timestamp);
```


