# Pentaho 7.0 Docker Image

## What is Pentaho?
[Pentaho](http://www.pentaho.com/) is a Business Intelligence (BI) software company that offers Pentaho Business Analytics, a suite of open source products which provide data integration, OLAP services, reporting, dashboarding, data mining and ETL capabilities.

## What is this?
Pentaho has [clearly stated](https://support.pentaho.com/hc/en-us/articles/210384343-Automated-deployment-solutions-Docker-Puppet-Chef-etc-) that they will not be providing autoamted deployment solutions for thier products.
This is a problem because the deployment process is quite complicated.

This docker project provides a much simpler deployment mechanism. It's not perfect, but with your help it can get better every day. :-)

It is based on some really splended work done by [Wellington Marinho](https://github.com/wmarinho/docker-pentaho).

## Requirements
- [PostgreSQL](https://www.postgresql.org/) 9.4
  - `postgres:9.4-alpine` worked nicely for me

## How to run
Start a PostgreSQL server
```
docker run -d -p 5432:5432 --name postgres postgres:9.4-alpine
```

Run once for testing and auto-clean container
```
docker run --rm -it --link postgres:postgres -e PGHOST=postgres -e PGUSER=postgres -e PGPASSWORD= -p 8080:8080 ca0abinary/docker-pentaho
```

Start in bash, prior to any scripts having executed, auto-clean (for debugging)
```
docker run --rm -it --link postgres:postgres -e PGHOST=postgres -e PGUSER=postgres -e PGPASSWORD= -p 8080:8080 --entrypoint bash ca0abinary/docker-pentaho
```

Run as a service
```
docker run -d --link postgres:postgres -e PGHOST=postgres -e PGUSER=postgres -e PGPASSWORD= -p 8080:8080 ca0abinary/docker-pentaho
```

## HyperSQL
It's possible to run without PostgreSQL and only use the hsqldb.
If you want your data to be preserved in the event of container loss, you should keep it in a data container or volume map.

Example:
```
docker run --rm -it -v /mnt/nfs-share/pentaho/hsqldb:/opt/pentaho/server/pentaho-server/data/hsqldb -p 8080:8080 ca0abinary/docker-pentaho
```

## Environment variables
- `PGHOST` The hostname or IP of the postgresql server
- `PGPORT` The port number of the postgreql service `(default 5432)`
- `PGDATABASE` The database name to use `(default postgres)`
- `PGUSER` The admin username (used for creating databases / database users) `(default pgadmin)`
- `PGPASSWORD` The admin password `(default pgadmin.)`

## Problems
Quartz and Hibernate continue to live on the internal [HyperSQL](http://hsqldb.org/) database as despite changing the settings to work with PostgreSQL using the provided documentation I'm not having success.
(`java.sql.SQLException: No suitable driver`)

The databases will be created in PostgreSQL, just not used.

Feel free to examine the `scripts/setup_postgresql.sh` and post a PR if you can fix it!
