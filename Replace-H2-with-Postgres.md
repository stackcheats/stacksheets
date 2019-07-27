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

### Pull

> Prerequisites: Docker installed in your environment. You can follow the Docker docs to get it done.

Follow the given steps to set up Postgres using Docker in your dev environments. For this demo, we will be using **Postgres - 9.6.14** and **WSO2 Identity Server 5.7**.

The following command is used to pull the Postgres image from DockerHub for the tag 9.6.14

```shell
docker pull postgres
```

### Run

After a successful pull, execute the below command to create and run a docker container for our Postgres DB image

```shell
docker run --name <CONTAINER_NAME> -e POSTGRES_PASSWORD=hydrogen -p 5432:5432 -d -v $HOME/docker/volumes/postgres:/var/lib/postgresql <IMAGE>
```

We can use the following command to move into the PSQL terminal session

```shell
docker run --name postgres-container -e POSTGRES_PASSWORD=hydrogen -p 5432:5432 -d -v $HOME/docker/volumes/postgres:/var/lib/postgresql postgres
```

Given below are the basic `start` and `stop` commands for Docker containers

#### Start & Stop

```shell
docker start <CONTAINER_NAME>
```

```shell
docker stop <CONTAINER_NAME>
```

### PSQL

```shell
docker exec -ti <CONTAINER_NAME> psql -h <HOST> -U <USERNAME>
```

```shell
docker exec -ti postgres-container psql -h localhost -U postgres
```

## Configurations

### Postgres

To replace the default packaged H2 database with Postgres, initially, we have to create the databases and tables in Postgres. Find and execute the following PostgreSQL scripts to create and set up all necessary tables and indexes.

> You can use either any database tools like DBeaver or you can straightaway execute them using PSQL terminal

* `<IS>/dbscripts/postgresql.sql`
* `<IS>/dbscripts/identity/postgresql.sql`
* `<IS>/dbscripts/identity/uma/postgresql.sql`
* `<IS>/dbscripts/identity/stored-procedures/postgre/postgresql.sql`
* `<IS>/dbscripts/consent/postgresql.sql`

### WSO2 Identity Server

After setting up the databases, tables and indexes, now we have to install the Postgres JDBC connector (driver) inside our WSO2 Identity Server.
Download the Postgres driver from [here](https://jdbc.postgresql.org/download.html), and place it inside the `<IS>/repository/components/lib` directory.

Now, we have to configure our database connection settings in the Identity Server to connect to the Postgres. Make changes to the following XML files to change the connection from default H2 database to our newly created Postgres database.

* [`<IS>/repository/conf/datasources/master-datasources.xml`](#master-datasource)
* [`<IS>/repository/conf/identity/identity.xml`](#identity)
* [`<IS>/repository/conf/registry.xml`](#registry)

> We will be changing the user store from the default LDAP server to the JDBC store

* [`<IS>/repository/conf/user-mgt.xml`](#user-management)

> The following configurations and xml snippets have template connection strings. For example: `jdbc:postgresql://{host | localhost}:{port | 5432}/wso2carbon`. Replace and change the `host` and `port` segments with respective values.

#### Master DataSource

```xml
<!-- master-datasources.xml -->
<datasource>
    <name>WSO2_CARBON_POSTGRES_DB</name>
    <description>The datasource used for registry and user manager</description>
    <jndiConfig>
        <name>jdbc/WSO2CarbonPostgresDB</name>
    </jndiConfig>
    <definition type="RDBMS">
        <configuration>
            <url>jdbc:postgresql://{host | localhost}:{port | 5432}/wso2carbon</url>
            <username>{username | postgres}</username>
            <password>{password | hydrogen}</password>
            <driverClassName>org.postgresql.Driver</driverClassName>
            <maxActive>80</maxActive>
            <maxWait>60000</maxWait>
            <minIdle>5</minIdle>
            <testOnBorrow>true</testOnBorrow>
            <validationQuery>SELECT 1; COMMIT</validationQuery>
            <defaultAutoCommit>true</defaultAutoCommit>
            <validationInterval>30000</validationInterval>
        </configuration>
    </definition>
</datasource>
```

#### Identity

```xml
<!-- identity.xml -->
<JDBCPersistenceManager>
        <DataSource>
            <!-- Include a data source name (jndiConfigName) from the set of data
                sources defined in master-datasources.xml -->
            <!-- <Name>jdbc/WSO2CarbonDB</Name> -->

            <Name>jdbc/WSO2CarbonPostgresDB</Name>
        </DataSource>
        ...
```

#### Registry

```xml
<!-- registry.xml -->
<dbConfig name="wso2registry">
    <dataSource>jdbc/WSO2CarbonDB</dataSource>
</dbConfig>

<dbConfig name="govregistry">
    <dataSource>jdbc/WSO2CarbonPostgresDB</dataSource>
</dbConfig>
<remoteInstance url="https://{host | localhost}:{port | 9443|/registry">
    <id>gov</id>
    <cacheId>{username | postgres}@jdbc:postgresql://{host | localhost}:{port | 5432}/wso2carbon</cacheId>
    <dbConfig>govregistry</dbConfig>
    <readOnly>false</readOnly>
    <enableCache>true</enableCache>
    <registryRoot>/</registryRoot>
</remoteInstance>
<mount path="/_system/governance" overwrite="true">
    <instanceId>gov</instanceId>
    <targetPath>/_system/governance</targetPath>
</mount>
<mount path="/_system/config" overwrite="true">
    <instanceId>gov</instanceId>
    <targetPath>/_system/config</targetPath>
</mount>
```

#### User Management

```xml
<Configuration>
    ...
    <!-- <Property name="dataSource">jdbc/WSO2CarbonDB</Property> -->

    <Property name="dataSource">jdbc/WSO2CarbonPostgresDB</Property>
</Configuration>
```

Comment the LDAP User-Store-Manager configurations and uncomment the JDBC User-Store-Manager. This will change our default LDAP user store with JDBC user store.
