# TeamSpeak for Docker

[![ImageOrigin](https://images.microbadger.com/badges/version/kleberbaum/teamspeak.svg)](https://microbadger.com/images/kleberbaum/teamspeak "Get your own version badge on microbadger.com")
[![Docker Stars](https://img.shields.io/docker/stars/kleberbaum/teamspeak.svg)](https://hub.docker.com/r/kleberbaum/teamspeak/)
[![Docker Pulls](https://img.shields.io/docker/pulls/kleberbaum/teamspeak.svg)](https://hub.docker.com/r/kleberbaum/teamspeak/)
[![ImageLayers](https://images.microbadger.com/badges/image/kleberbaum/teamspeak.svg)](https://microbadger.com/#/images/kleberbaum/teamspeak)

Size optimised docker image based on [alpine](https://alpinelinux.org/).

Extensively tested on [netcup 2.99€/a VPS](https://www.netcup.de/bestellen/produkt.php?produkt=1710).

Build for [Ts3.Party](ts3server://ts3.party?port=9987).

#### Upstream Links

* Docker Registry @ [kleberbaum/teamspeak](https://hub.docker.com/r/kleberbaum/teamspeak/)
* GitHub @ [kleberbaum/docker-teamspeaky](https://github.com/kleberbaum/docker-teamspeak)

## What is TeamSpeak

TeamSpeak is proprietary voice-over-Internet Protocol (VoIP) software for audio communication between users on a chat channel, much like a telephone conference call. Users typically use headphones with a microphone. The client software connects to a TeamSpeak server of the user's choice, from which the user may join chat channels.

> [wikipedia.org/wiki/TeamSpeak](https://en.wikipedia.org/wiki/TeamSpeak)

![logo](https://raw.githubusercontent.com/kleberbaum/docker-teamspeak/54d169025092ad9f612a1647a5bc9e19fdbe56c6/logo.png)
&nbsp;

## How to use this image

### Default

```
$ docker run -d --name teamspeak \
  -p 9987:9987/udp \
  -p 30033:30033 \
  -p 10011:10011 \
  kleberbaum/teamspeak:latest
```
&nbsp;

### Persist data
```
docker run -d --name teamspeak \
  -v ${PWD}/config:/teamspeak/config \
  -v ${PWD}/files:/teamspeak/files \
  -v ${PWD}/logs:/teamspeak/logs \
  -v ${PWD}/data:/teamspeak/data \
  -p 9987:9987/udp \
  -p 30033:30033 \
  -p 10011:10011 \
  kleberbaum/teamspeak:latest
```
&nbsp;

### Docker-compose
```
version: '2'
services:
  teamspeak:
    image: kleberbaum/teamspeak:latest
    environment:
      - TS_DBPLUGIN=ts3db_mariadb
      - TS_DBSQLCREATEPATH=create_mariadb/
      - TS_DBHOST=mysql
      - TS_DBDATABASE=teamspeak
      - TS_DBPASSWORD=changeme
    volumes:
      - ./files:/teamspeak/files
      - ./logs:/teamspeak/logs
    links:
      - mysql
    ports:
      - 9987:9987/udp
      - 10011:10011
      - 30033:30033
  mysql:
    image: mariadb:latest
    environment:
      - MYSQL_DATABASE=teamspeak
      - MYSQL_ROOT_PASSWORD=b3secure
    volumes:
      - ./mysql:/var/lib/mysql
```
&nbsp;

## Configuration

### Commandline Parameters

This images uses docker entrypoint for starting the server. Cmd can be used to append paramters to the start command. Full list of parameters can be found at the official [quick-start guilde](http://media.teamspeak.com/ts3_literature/TeamSpeak%203%20Server%20Quick%20Start.txt). A few parameters are predefined to have a simpler docker setup:

```
licensepath=config/ createinifile=1 inifile=config/ts3server.ini query_ip_whitelist=config/query_ip_whitelist.txt query_ip_blacklist=config/query_ip_blacklist.txt dbpluginparameter=config/ts3db.ini
```

You can override the predefined parameter with your own. For example by enabling log append instead of creating a new log file on each startup:

```
$ docker run -d --name teamspeak -v ${PWD}/logs:/teamspeak/logs kleberbaum/teamspeak:latest logappend=1
```
&nbsp;

### File ts3server.ini

Instead of using commandline parameters a config file can be used. This file is located in `data/ts3server.ini`. An example of a config file can be found in the following snippet:

```
machine_id=
default_voice_port=1337
voice_ip=0.0.0.0
licensepath=config/
filetransfer_port=30033
filetransfer_ip=0.0.0.0
query_port=10011
query_ip=0.0.0.0
query_ip_whitelist=config/query_ip_whitelist.txt
query_ip_blacklist=config/query_ip_blacklist.txt
dbplugin=ts3db_sqlite3
dbpluginparameter=config/ts3db.ini
dbsqlpath=sql/
dbsqlcreatepath=create_sqlite/
dbconnections=10
logpath=logs
logquerycommands=0
dbclientkeepdays=30
logappend=0
query_skipbruteforcecheck=0
```
&nbsp;

### File ts3db.ini

If you want to use MariaDB/MySQL instead of the default SQLite you have to define a own config file with the connection settings. This config file is located in `data/ts3db.ini`. An example of a config file can be found in the following snippet:

```
[config]
host=localhost
port=3306
username=teamspeak
password=x5gUjs
database=ts3db
socket=
```
&nbsp;

### Environment variables

This image supports environment variables for the TeamSpeak server.

&#x1F534; Mapped to ts3server.ini &#x1F537; Mapped to ts3db.ini

|Variable|Default|
|-----------|---------|
|&#x1F534; TS3SERVER_LICENSEPATH|/opt/ts3server/|
|&#x1F534; TS3SERVER_NO_PERMISSION_UPDATE|0|
|&#x1F534; TS3SERVER_MACHINE_ID|0|
|&#x1F534; TS3SERVER_CREATE_DEFAULT_VIRTUALSERVER|1|
|&#x1F534; TS3SERVER_VOICE_IP|0.0.0.0,0::0|
|&#x1F534; TS3SERVER_DEFAULT_VOICE_PORT|9987|
|&#x1F534; TS3SERVER_FILETRANSFER_IP|0.0.0.0,0::0|
|&#x1F534; TS3SERVER_FILETRANSFER_PORT|30033|
|&#x1F534; TS3SERVER_QUERY_IP|0.0.0.0,0::0|
|&#x1F534; TS3SERVER_QUERY_PORT|10011|
|&#x1F534; TS3SERVER_QUERY_PROTOCOLS|raw|
|&#x1F534; TS3SERVER_QUERY_BUFFER_MB|20|
|&#x1F534; TS3SERVER_QUERY_TIMEOUT|300|
|&#x1F534; TS3SERVER_QUERY_SSH_RSA_HOST_KEY|ssh_host_rsa_key|
|&#x1F534; TS3SERVER_IP_WHITELIST|query_ip_whitelist.txt|
|&#x1F534; TS3SERVER_IP_BLACKLIST|query_ip_blacklist.txt|
|&#x1F534; TS3SERVER_QUERY_SKIPBRUTEFORCECHECK|0|
|&#x1F534; TS3SERVER_SERVERQUERYDOCS_PATH|/opt/ts3server/serverquerydocs/|
|&#x1F534; TS3SERVER_CLEAR_DATABASE|0|
|&#x1F534; TS3SERVER_DB_SQLCREATEPATH|create_sqlite|
|&#x1F534; TS3SERVER_DB_PLUGIN|ts3db_sqlite3|
|&#x1F534; TS3SERVER_DB_PLUGINPARAMETER|/var/run/ts3server/ts3db.ini|
|&#x1F534; TS3SERVER_DB_SQLPATH|/opt/ts3server/sql/|
|&#x1F537; TS3SERVER_DB_SOCKET|&lt;empty&gt;|
|&#x1F537; TS3SERVER_DB_HOST|0.0.0.0,0::0|
|&#x1F537; TS3SERVER_DB_PORT|3306|
|&#x1F537; TS3SERVER_DB_NAME|ts3|
|&#x1F537; TS3SERVER_DB_USER|teamspeak|
|&#x1F537; TS3SERVER_DB_PASSWORD|&lt;empty&gt;|
|&#x1F534; TS3SERVER_DB_CONNECTIONS|10|
|&#x1F534; TS3SERVER_DB_CLIENTKEEPDAYS|30|
|&#x1F534; TS3SERVER_DB_WAITUNTILREADY|30|
|&#x1F534; TS3SERVER_DB_LOGKEEPDAYS|90|
|&#x1F534; TS3SERVER_LOG_APPEND|0|
|&#x1F534; TS3SERVER_LOG_PATH|/var/ts3server/logs|
|&#x1F534; TS3SERVER_LOG_QUERY_COMMANDS|0|
&nbsp;

## Differences from phaldan/docker-teamspeak

* Uses a clean alpine:latest as base
* Uses a init (krallin/tini)
* Build with a clean Dockerfile
* Used by ts3.party ^^
