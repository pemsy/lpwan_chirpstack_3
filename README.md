# lpwan_chirpstack_3

## Table of contents
* [Requirements](#requirements)
* [Install postgresql](#install-postgresql)
* [Creating an user and database](#creating-an-user-and-database)
* [ChirpStack Network Server Debian repository](#technologies)
* [Install ChirpStack Network Server](#setup)

## Requirements
Project is created with:
* MQTT broker
```
sudo apt install mosquitto
```
* PostgreSQL database 9.5+
```
sudo apt install postgresql
```
* Redis database
```
sudo apt install redis-server
```
* pq_trgm and hstore extension
```
-- change to the ChirpStack Application Server database
\c chirpstack_as

-- enable the extensions
create extension pg_trgm;
create extension hstore;

-- exit the prompt
\q
```



# Creating an user and database
## Network Server
```
sudo -u postgres psql

-- create the chirpstack_ns user with password 'dbpassword'
create role chirpstack_ns with login password 'dbpassword';

-- create the chirpstack_ns database
create database chirpstack_ns with owner chirpstack_ns;

-- exit the prompt
\q
```
Verify user and password
```
psql -h localhost -U chirpstack_ns -W chirpstack_ns
```
## Application Server
```
-- create the chirpstack_as user
create role chirpstack_as with login password 'dbpassword';

-- create the chirpstack_as database
create database chirpstack_as with owner chirpstack_as;

-- enable the trigram and hstore extensions
\c chirpstack_as
create extension pg_trgm;
create extension hstore;

-- exit the prompt
\q
```
Verify user and password
```
psql -h localhost -U chirpstack_as -W chirpstack_as
```

# ChirpStack Network and Application Server Debian repository
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1CE2AFD36DBCCA00
```
```
sudo echo "deb https://artifacts.chirpstack.io/packages/3.x/deb stable main" | sudo tee /etc/apt/sources.list.d/chirpstack.list
sudo apt update
```

# Install ChirpStack Network Server
```
sudo apt install chirpstack-network-server
```

## After installation, modify the configuration file which is located at /etc/chirpstack-network-server/chirpstack-network-server.toml.

## Settings you probably want to set / change:

##postgresql.dsn
##postgresql.automigrate
##network_server.net_id
##network_server.band.name
##metrics.timezone

## Starting ChirpStack Network Server
### init.d
```
sudo /etc/init.d/chirpstack-network-server [start|stop|restart|status]
```
### systemd
```
sudo systemctl [start|stop|restart|status] chirpstack-network-server
```


## ChirpStack Network Server log output

### init.d
```
tail -f /var/log/chirpstack-network-server/chirpstack-network-server.log
```

### systemd
```
journalctl -u chirpstack-network-server -f -n 50
```

## Configfile Network Server
```
chirpstack-network-server configfile --config chirpstack-network-server-old.toml > chirpstack-network-server-new.toml
```

# Install ChirpStack Application Server
sudo apt-get install chirpstack-application-server

postgresql.dsn
postgres://chirpstack_as:dbpassword@localhost/chirpstack_as?sslmode=disable

application_server.external_api.jwt_secret

Starting ChirpStack Application Server
init.d
sudo /etc/init.d/chirpstack-application-server [start|stop|restart|status]
systemd
sudo systemctl [start|stop|restart|status] chirpstack-application-server

ChirpStack Application Server log output
init.d
tail -f /var/log/chirpstack-application-server/chirpstack-application-server.log
systemd
journalctl -u chirpstack-application-server -f -n 50


HTTP
Web-interface http://localhost:8080/
API http://localhost:8080/api

HTTPS
Web-interface https://localhost:8080/
API https://localhost:8080/api

## Configfile Application Server
chirpstack-application-server configfile --config chirpstack-application-server-old.toml > chirpstack-application-server-new.toml
