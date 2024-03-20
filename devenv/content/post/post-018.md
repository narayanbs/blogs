---
title: "PostgreSql"
date: 2022-06-19T10:16:02+05:30
publishdate: 2022-06-19
lastmod: 2022-06-19
tags: [database, docker]
---
### Installation - Docker
```bash
# Pull docker image from dockerhub
$ sudo docker pull postgres
# check all existing docker images
$ sudo docker images
# start postgres instance
$ sudo docker run -d --name postgres-server -p 5432:5432 -e POSTGRES_PASSWORD=slayer postgres
# check all running docker containers
$ sudo docker ps
# Connect to postgres
$ sudo docker exec -it postgres-server psql -U postgres
```
### Create database objects
```bash
# create database (; semicolon is required)
> create database ps_database;
# connect to database
> \c ps_database
# create table
> 
# quit
>\q
```

### logs 
```bash
$ sudo docker logs postgres-server
```

### stop postgres container
```bash
$ sudo docker stop postgres-server
```
### start/restart postgres container
```bash
$ sudo docker start postgres-server
$ sudo docker restart postgres-server
```
