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

Class mediators provide an extended platform to develop user-specific implementations and custom mediation logic in the form of Java classes. In this sheet, we will be developing a custom class mediator to read, manipulate and convert (change) a simple payload to meet our requirements.

<p class="quote text-secondary">
    Class mediators are designed to use on user-specific custom implementations when there are no other built-in mediators to solve the requirement
</p>

### Requirement

The requirement is to develop a simple custom class mediator to read and change the value of the `name` attribute of a given payload from "medium" to "Athiththan".

The actual payload and the expected converted (changed) payload formats are given below.

```json
-------------------------------------
// request payload || original
{
    "post": "WSO2 Class Mediator: Convert Payload",
    "dev": {
        "name": "medium"
    }
}
-------------------------------------
// mediated payload || tconverted
{
    "post": "WSO2 Class Mediator: Convert Payload",
    "dev" : {
        "name": "Athiththan"
    }
}
-------------------------------------
```

## Configure

To fabricate our custom class mediator or to generate a maven project, we can follow either

* using the given `pom.xml` file to generate a maven project
* using a custom-developed maven-archetype to generate a maven project

Following sections presents the steps on using given `pom.xml` and using custom developed-archetype to generate maven projects for our custom implementation.

### pom.xml

Create a maven quick-start project and replace the dependencies with the following

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

Clone or download the custom-developed [maven-archetype](https://github.com/athiththan11/WSO2-Mediators-Maven-Archetype) and run the following command to build and install the archetype locally on your machine to generate boilerplates for our custom class mediators.

<br>

<a class="ul-disabled" href="https://github.com/athiththan11/WSO2-Mediators-Maven-Archetype">
    <div class="card">
        <div class="card-horizontal">
            <div class="card-body py-4 px-5">
                <h4 class="card-title mt-4">WSO2 Mediators Maven Archetype</h4>
                <p>Maven Archetype for WSO2 Class Mediators</p>
                <footer class="blockquote-footer">
                    <small class="text-muted">
                        athiththan11
                    </small>
                </footer>
            </div>
            <div class="img-square-wrapper">
                <img class="rounded mb-0" width="220.172" src="https://avatars3.githubusercontent.com/u/29927177?s=460&v=4" alt="">
            </div>
        </div>
    </div>
</a>

<br>

```shell
mvn clean install
```

Then, execute the following command to generate a Maven project (boilerplate)

> Change the command according to your required Group ID and Artifact ID arguments

```shell
mvn archetype:generate  -DarchetypeGroupId=com.athiththan.wso2     \
                        -DarchetypeArtifactId=wso2-mediators       \
                        -DarchetypeVersion=1.0.0                   \
                        -DgroupId=<Your Group ID>                  \
                        -DartifactId=<Your Artifact ID>
```

## Development

### Class Mediator

We can develop a custom Class mediator by implementing the `Mediator` class or by extending the `AbstractMediator` class. In this sheet, We will discuss developing a custom class mediator by extending the `org.apache.synapse.mediators.AbstractMediator` class.

> The custom mediation logic has to defined inside the `mediate()` method to get execute

A simple class mediator implementation to convert the name attribute from `medium` to `Athiththan` is presented below.

`gist:athiththan11/71e8c5baa662c34b46a008ea824332a9#CustomMediator.java`

## Build, Run & Test

### Build

Execute the following command from our project root folder to build and package our custom mediator implementation.

```shell
mvn clean package
```

As a result, maven will package our implementation as a JAR (Java Archive) artifact, which can be found inside the `/target` folder.

### Run & Test

> For demonstration purposes

I will be mocking and using the `Token_Endpoint` of WSO2 API Manager (2.6.0) and will be referring to the installation path of WSO2 API-M as `<API-M_HOME>` and our JAR artifact as `CM-IMPL-JAR`.

* Copy the `CM-IMPL_JAR` from the `/target` folder and paste it inside the `<API-M_HOME>/repository/components/lib/` directory
* Navigate to `<API-M_HOME>/repository/deployment/server/synapse-configs/default/api/` and open and edit the inSequence tag of the `_TokenAPI_.xml` file as follows

```xml
<inSequence>
    <!-- <property name="uri.var.portnum" expression="get-property('keyManager.port')"/>
    <property name="uri.var.hostname" expression="get-property('keyManager.hostname')"/>
    <send>
        <endpoint>
            <http uri-template="https://{uri.var.hostname}:{uri.var.portnum}/oauth2/token">
                <timeout>
                    <duration>60000</duration
                    <responseAction>fault</responseAction>
                </timeout>
            </http>
        </endpoint>
    </send> -->
    <!-- convert dev.name to athiththan -->
    <class name="<YOUR PACKAGE NAME>.<CLASS NAME>" />
    <respond />
</inSequence>
```

> Replace `<YOUR PACKAGE NAME>.<CLASS NAME>` with the actual package name and your Java class name.

For example …

If you have implemented your class mediator implementation in a Java class named `CustomClassMediator` inside the `com.sample.demos package`, then it comes as `com.sample.demos.CustomClassMediator` resulting `<class name="com.sample.demos.CustomClassMediator" />`.

After pasting our `CM-IMPL_JAR` and editing the `_TokenAPI_.xml` file, start the WSO2 API Manager instance using the following command.

```shell
sh wso2server.sh
```

Use the following cURL command to execute the Token Endpoint of the WSO2 API Manager instance by replacing the `<Your Host>` tag with respective host value

```shell
curl -k --request POST \
    -d '{"post":"WSO2 Class Mediator: Convert Payload","dev":{"name":"medium"}}' \
    -H "Content-Type: application/json" \
    https://<YOUR HOST>:8243/token
```

After successful execution, we can observe the response as follows

```json
{
    "post":"WSO2 Class Mediator: Convert Payload",
    "dev":{
        "name":"Athiththan"
    }
}
```

## GitHub

<br>

<a class="ul-disabled" href="https://github.com/athiththan11/Revamp-Mediator">
    <div class="card">
        <div class="card-horizontal">
            <div class="card-body py-4 px-5">
                <h4 class="card-title mt-4">Revamp Mediator</h4>
                <p>A custom mediation logic to convert payload attribute values using WSO2 Class Mediator extension</p>
                <footer class="blockquote-footer">
                    <small class="text-muted">
                        athiththan11
                    </small>
                </footer>
            </div>
            <div class="img-square-wrapper">
                <img class="rounded mb-0" width="220.172" src="https://avatars3.githubusercontent.com/u/29927177?s=460&v=4" alt="">
            </div>
        </div>
    </div>
</a>

<hr class="three--dots"/>
