# ChapoWebServers
Webservices running on Roc Chip &amp; Rpi3

## Description

Proxified web servers

## Configuration 

You need to edit `.env` with your own configuration. See below the explanation of variables :

- `${SERVER_NAMEXX}`        Server domain name (with XX the number you want)
- `${DB_USER}`              User used to log the WordPress DB
- `${DB_PASSWORD}`          Password of the user
- `${DB_ROOT_PASSWORD}`     Password for root on DB
- `${WORDPRESS_DB_NAME}`    DB name used by WordPress
- `${WORDPRESS_VERSION}`    Version of the container WordPress
- `${MYSQL_VERSION}`        Version of the container Mysql
- `${PROXY_VERSION}`        Version of the container Proxy

## Installation

Required packages are:

- docker
- docker-compose

To launch the platform, as simple as docker is, just run
```
docker-compose up -d
```
It will fetch locally missing images and start each services.

To watch services logs
```
docker-compose logs [service]
```

To stop & start services
```
docker-compose stop [service]
docker-compose start [service]
```
Omitting service will stop all services

To fully destroy containers
```
docker-compose down
```

To force rebuild images
```
docker-compose up --force-recreate --build -d
```

## SubProjects

TO DO