# ChapoWebServers
Webservices running on Roc Chip &amp; Rpi3

## Description

This tiny environnement relies on arm64v8 repo on http://hub.docker.com/ where container images are designed to fit the architecture of "libre computer" like RaspberryPi.
The aim is to create a small scallable infrastructure allowing testing dockerized webservices online

## Todo / Changelog

See ongoing changes on [Trello](https://trello.com/b/HhZisvwy)

- [x] Prepare hardware & network
- [x] Install docker tools on 3 chips
- [x] Start standalone containers
- [x] Configure launch with docker-compose
- [x] Start container behind reverse proxy
- [x] Add another service to the pool
- [x] Encrypt with SSL certificates
- [ ] Normalize addition of services to existing configuration
- [ ] Deploy containers through docker swarm
- [ ] Add metrics
- [ ] Scale up by adding one more chip to the swarm

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

To launch the plateform, as simple as docker is, just run
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
docker-compose up--force-recreate --build -d
```

## About the project

It all started when I bought the first version of RaspberryPi computer. It was the best way for me to build a server to answer some of my needs. The first services installed (BareOS) were:

- AirPlay
- TimeCapsule
- NFS
- Owncloud
- transmission-torrent
- gitlab

The Pi was used as a Streaming plateform (audio, movies, documents), backup plateform, seedbox and dev plateform. All these services ran well on the tiny chip despite the few performances. But, the maintenance was a bit tough when it came to debug. Thus, sadly, some services stopped working. Then I moved to another location and I shut the Pi off.

Later, I saw a project of a raspi dashcam and I started thinking about doing it myself, which I never did due to a lack of time.

Recently, I heard about docker and containers at school and at work. From that time I am really interested in acquiring knowledge about this tech. As I am still a student and I have no big income, I set my sight on new generation chips. I am challenging myself to cluster their hardware and to deploy containers freely over a scalable plateform made by my own.
