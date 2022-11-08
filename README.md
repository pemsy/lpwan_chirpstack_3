# lpwan_chirpstack_3

#Creating an user and database

sudo -u postgres psql

-- create the chirpstack_ns user with password 'dbpassword'
create role chirpstack_ns with login password 'dbpassword';

-- create the chirpstack_ns database
create database chirpstack_ns with owner chirpstack_ns;

-- exit the prompt
\q

psql -h localhost -U chirpstack_ns -W chirpstack_ns


#ChirpStack Network Server Debian repository
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1CE2AFD36DBCCA00

sudo echo "deb https://artifacts.chirpstack.io/packages/3.x/deb stable main" | sudo tee /etc/apt/sources.list.d/chirpstack.list
sudo apt update

#Install ChirpStack Network Server
sudo apt install chirpstack-network-server

##After installation, modify the configuration file which is located at /etc/chirpstack-network-server/chirpstack-network-server.toml.

##Settings you probably want to set / change:

##postgresql.dsn
##postgresql.automigrate
##network_server.net_id
##network_server.band.name
##metrics.timezone

#Starting ChirpStack Network Server
##init.d
sudo /etc/init.d/chirpstack-network-server [start|stop|restart|status]
#systemd
sudo systemctl [start|stop|restart|status] chirpstack-network-server


ChirpStack Network Server log output

init.d
tail -f /var/log/chirpstack-network-server/chirpstack-network-server.log

systemd
journalctl -u chirpstack-network-server -f -n 50

#Configfile
chirpstack-network-server configfile --config chirpstack-network-server-old.toml > chirpstack-network-server-new.toml

