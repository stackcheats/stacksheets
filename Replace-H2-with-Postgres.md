---
title: Replace H2 with Postgres
intro: 'WSO2 Identity Server (5.7): Replace Default H2 DB with Postgres DB'
short: Guide to replace H2 with Postgres DB
category: WSO2
tags: ['wso2', 'wso2-identity-server', 'h2', 'postgres']
author: Athiththan
weight: -1
updated: 2019-07-21
date: '2019-07-21T12:00:00.284Z'
---

## Docker: Postgres

### Docker

### Postgres

#### Pull

```shell
docker pull postgres
```

#### Run

```shell
docker run --name <CONTAINER_NAME> -e POSTGRES_PASSWORD=hydrogen -p 5432:5432 -d -v $HOME/docker/volumes/postgres:/var/lib/postgresql <IMAGE>
```

```shell
docker run --name postgres-container -e POSTGRES_PASSWORD=hydrogen -p 5432:5432 -d -v $HOME/docker/volumes/postgres:/var/lib/postgresql postgres
```

#### Start & Stop

```shell
docker start <CONTAINER_NAME>
```

```shell
docker stop <CONTAINER_NAME>
```

#### PSQL

```shell
docker exec -ti <CONTAINER_NAME> psql -h <HOST> -U <USERNAME>
```

```shell
docker exec -ti postgres-container psql -h localhost -U postgres
```

## WSO2 Identity Server

Download Postgres driver from [here](https://jdbc.postgresql.org/download.html)

### MasterSource

### Registry

### Identity
