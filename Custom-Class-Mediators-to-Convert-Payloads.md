---
title: 'Custom Class Mediators to Convert Payloads'
intro: 'WSO2 Class Mediators: Change Values of a Payload Attribute'
short: A Custom Class Mediator Implementation
category: WSO2
tags: ['wso2', 'class-mediators']
author: Athiththan
weight: -1
updated: 2019-07-17
date: '2019-07-17T13:25:00.284Z'
---

## Intro

<p class="quote text-secondary">
    Class Mediators used for user-specific custom developments when there are no built-in mediators to solve the requirements
</p>

Class Mediators enables and provides an extended platform to develop custom mediation logic in the form of Java classes and to apply them in various sequences. In this technical writing, I will be navigating you all through on a “how-to” develop a simple custom class mediator to read, manipulate and convert the payloads to meet your custom requirements.

To simply put, we will be developing a custom **Class Mediator** to read and change the value of the name attribute of a given payload from “medium” to “Athiththan”. Given below are the actual payload and the expected converted payload formats …

```json
-------------------------------------
// request payload || the original
{
    "post": "WSO2 Class Mediator: Convert Payload",
    "dev": {
        "name": "medium"
    }
}
-------------------------------------
// mediated payload || the converted
{
    "post": "WSO2 Class Mediator: Convert Payload",
    "dev" : {
        "name": "Athiththan"
    }
}
-------------------------------------
```

## Configure

We’ll create a maven project to design and develop our custom class mediator to convert the payloads. You can either follow …

* The given pom.xml file to replace and build the maven project
* Clone, build, install and use the maven archetype designed to generate boilerplate for WSO2 Class Mediators

### pom.xml

Create a maven quick-start project and paste the following dependencies, repositories and build tags to generate and develop custom class mediators.

```xml
<!-- change the packaging to bundle (from jar) to support OSGi -->
<packaging>jar</packaging>

<dependencies>
    <!-- apache synapse and related dependencies -->
    <dependency>
        <groupId>org.apache.synapse</groupId>
        <artifactId>synapse-core</artifactId>
        <version>2.1.7-wso2v60</version>
    </dependency>
    <dependency>
        <groupId>org.apache.axis2.wso2</groupId
        <artifactId>axis2</artifactId
        <version>1.6.1.wso2v20</version>
    </dependency>
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId
        <version>1.1.1</version>
    </dependency>

    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<repositories>
    <repository>
        <id>wso2-nexus</id
        <url>http://maven.wso2.org/nexus/content/groups/wso2-public/</url>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>daily</updatePolicy
            <checksumPolicy>ignore</checksumPolicy>
        </releases>
    </repository>
</repositories>

<pluginRepositories>
    <pluginRepository>
        <id>wso2-nexus</id
        <url>http://maven.wso2.org/nexus/content/groups/wso2-public/</url>
        <releases>
            <enabled>true</enabled
            <updatePolicy>daily</updatePolicy
            <checksumPolicy>ignore</checksumPolicy>
        </releases>
    </pluginRepository>
</pluginRepositories>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId
            <artifactId>maven-compiler-plugin</artifactId
            <version>2.0</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>

        <!-- build plugin for OSGi Bundle support -->
        <plugin>
            <groupId>org.apache.felix</groupId>
            <artifactId>maven-bundle-plugin</artifactId
            <version>2.3.4</version>
            <extensions>true</extensions>
            <configuration>
                <instructions>
                    <Bundle-SymbolicName>
                        ${groupId}
                    </Bundle-SymbolicName>
                    <Bundle-Name>${groupId}</Bundle-Name
                    <Export-Package>
                        ${groupId}.*,
                    </Export-Package>
                    <Import-Package>
                        *; resolution:=optional
                    </Import-Package>
                </instructions>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### Build using Maven Archetype

Clone or download the following repository to your machine and run the following command to build and install the maven archetype locally in your machine.

```shell
mvn clean install
```

The above command will download all necessary dependencies and installs the defined maven archetype locally in your machine to generate boilerplate to kick-start the implementations of custom **Class Mediators**.

Use the following command to generate a maven project using the archetype.

> Don’t forget to change the command by adding your Group ID and Artifact ID in related fields.

```shell
mvn archetype:generate  -DarchetypeGroupId=com.athiththan.wso2     \
                        -DarchetypeArtifactId=wso2-mediators       \
                        -DarchetypeVersion=1.0.0                   \
                        -DgroupId=<Your Group ID>                  \
                        -DartifactId=<Your Artifact ID>
```

## Development

### Class Mediator
