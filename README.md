# pg-logical-replication

## Set replication level

postgresql.conf:
```code
wal_level = logical			# minimal, replica, or logical
```
restart postgres now

## Configure the publisher db

### Create publication
```
create publication example_pub for TABLE users, clients;
```
### Create replication slot
```
SELECT pg_create_logical_replication_slot('example_pub_slot', 'pgoutput');
```

## Configure subscriber db

### Create the tables to be replicated:
```
create table users (...) 
create table users (clients) 
```
### Create the subscription
```

CREATE SUBSCRIPTION example_sub
    CONNECTION 'host=127.0.0.1 port=5432 user=postgres password=yourpassword dbname=pub_db_name connect_timeout=10'
    PUBLICATION example_pub
    WITH (connect = true, enabled = true, copy_data = true, create_slot = false , slot_name = 'example_pub_slot', synchronous_commit = 'off');
```

## Check the replication status on the subscriber side
```
select *
from pg_stat_replication;
```
Consult the postgres server log to make sure that there's no errors

# Relevant documentation

[Postgres Logical Replication](https://www.postgresql.org/docs/current/logical-replication.html)
