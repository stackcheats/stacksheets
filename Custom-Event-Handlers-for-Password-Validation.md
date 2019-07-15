---
title: Custom Event Handlers for Password Validation
intro: 'WSO2 Identity Server: Event Handlers + Java WatchService'
short: something here
category: WSO2
tags: ['wso2', 'wso2-identity-server', 'event-handlers']
author: Athiththan
weight: -1
updated: 2019-01-17
date: '2019-06-19T22:12:03.284Z'
---

## A Story Guide

Stackcheats is a cheat-blog platform where developers and technical writers can create & write tech-cheats by owning user accounts. More importantly, Stackcheats has been using the WSO2 Identity Server as its identity and access management tool to manage user registrations and management tasks.

A tech-developer named “Athiththan” have been requested to develop a custom component to validate the password against a list of defective passwords when users trying to update (renew).

## Development

<p class="quote text-secondary">
    WSO2 Identity Server (5.3.0 ≤ onwards) now supports to handle published events using an integrated event framework by extending `AbstractEventHandler`
</p>

So, Athiththan chose to develop the custom component as an event-handler listening to different password-update events to trigger and to validate the given password.
We can use the following custom maven archetype to generate boilerplate to extend and implement custom event-handlers for WSO2 Identity Server.

### Handle Event

The AbstractEventHandler contains a method named handleEvent() which gets triggered whenever a pre-registered event is made upon the Identity Server. So, for this custom implementation, he will be using the following two publish events …

- <pre>PRE_UPDATE_CREDENTIAL</pre>
- <pre>PRE_UPDATE_CREDENTIAL_BY_ADMIN</pre>

`gist:athiththan11/2b8b5690bf65b7d16eb2973bbaa22442#DefectivePasswordValidatorEventHandler.java`

### Password Validator

Given below is the implementation to validate the given password against a list of defective passwords. The given implementation reads all the defective passwords from a text file named passwords.txt which is placed inside the `/<IS_HOME>/deployments/server/defective` folder.

> For testing purposes, the given implementation can be modified to work with pre-defined list of defective passwords using ArrayList<>.

`gist:athiththan11/91f041fb6285235d2708580e914ce13c#DefaultDefectivePasswordValidator.java`

### OSGi Component

The custom event-handler has to be registered as a service component (OSGi) with the Identity Server components to work flawlessly. In order to register the custom implementation as a service component, he wrote a ServiceComponent class containing both activate and deactivate methods to register it with the Bundle Context.

`gist:athiththan11/b1156885c454c5613fbb953c21fb7ca4#DefectivePasswordValidatorServiceComponent.java`

## Prepare

### Defective Passwords Text File

Create a file called passwords.txt and add all defective-passwords (cracked passwords) at each line. These defined cracked passwords are the values, will be used to validate the entered password.

Create a folder named defective inside the <IS_HOME>/repository/deployment/server directory and place the above created passwords.txt file in it.

### WSO2 Identity Server

Navigate to <IS_HOME>/repository/conf/identity directory and open the identity-event.properties file and add the following lines at the bottom to subscribe to the published events.

> Please update the digit value according to your `identity-event.properties` file (last used value + 1)
>
> The given `module.name` value is the same value which configured in the `getName()` method in `DefectivePasswordValidatorEventHandler` class

`gist:athiththan11/fc5bc7e1b8e46124ed0fa993a4324580#identity-event.properties`

## Build, Run & Test

### Build

You can clone or download the custom event-handler implementation project done by Athiththan from GitHub and build it using the following command …

```shell
mvn clean package
```

### Run

After a successful build, copy the `defective-password-validator-1.0.0.jar` artifact and place it inside the `<IS_HOME>/repository/components/dropins` folder.

Start your WSO2 Identity Server by navigating to the `<IS_HOME>/bin` folder and executing the following command …

```shell
sh wso2server.sh
```

### Test

After server startup, navigate to `https://<IP_ADDRESS>:9443/dashboard` and log in using `admin` as both username and password. Click on the _“View Details”_ button on the _“Change Password”_ section and type your current password and enter the new password as one of the defective passwords and try on.

<p class="quote text-secondary">
    Prepare your extraordinary core (- brain) to process different events exceptionally without exceptions
    <br/>
    — Athiththan
</p>

<hr class="three--dots"/>
