---
title: 'Preview Deck | Troubleshoot Guide'
date: '2021-05-18'
intro: 'A Troubleshooting guide for Throttling'
short: Sample Reveal Content in StackCheats

tags: ['wso2', 'reveal', 'sample']
seo: 'A sample reveal JS content'
reveal: true
---

# 👋 Preview Deck

--h--

## Troubleshoot Throttling

This reveal deck contains the fundamental steps and instructions to troubleshoot few well-known issues in API Throttling of WSO2 API Manager.

> This deck is drafted based on API Manager v3.2.0, however, this can referenced for v3.1.0, and v3.0.0 versions

> *Move across the deck using either arrow key on the keyboard, of by clicking the arrow icons in the bottom-right corner*

Let's get started!!!

reveal-note:
> Use the arrow keys (`up`,`right`, `down`, and `left`) on the keyboard to navigate through the deck
> <br/><br/>
> Press `o` to show an overview of the deck
> <br/><br/>
> Press `f` for full-screen mode

--h--

Are you having any issues while publishing a (custom) policy from the `Admin` portal?

If **yes**, go through the `below`-given steps to verify the configurations in your environment.
If **no**, move to the `right`.

--v--

A probable cause for this behavior can be a misconfiguration of the `Service URL` of the `Policy Deployer`. Open the `<publisher>/repository/conf/deployment.toml` and validate the following

```toml
[apim.throttling]
service_url = "https://tm-lb-hostname:9443/services/"
```

> The `service_url` must point to the Traffic Manager's `services` endpoint. This is used to publish and deploy the execution policies in Traffic Manager. If the endpoint is not reachable or misconfigured, there will be errors logged in the Publisher.

--v--

Further, if you are using different user credentials (`username` and `password`) for each node, please perform the following configurations in the Publisher's `deployment.toml` to authenticate with the Traffic Manager without any failures.

```toml
[apim.throttling.policy_deploy]
username = "traffic-manager-admin-username"
password = "traffic-manager-admin-password"
```

--h--

> We assume that you don't have any issues with creating or publishing (custom) policies from the Admin portal. But, experiencing inconsistent behavior during the runtime.

If you are experiencing errors in the Gateway node during runtime, scroll `down`. If not, move to the `right`.

reveal-note:

Advanced Throttling enables an async communication between Gateway and Traffic Manager. During runtime, the Gateway collects and sends events to the Traffic Manager to process decisions.

> If the API resource is unsecured, a default `Unauthenticated` subscription policy will be used to evaluate the subscription level throttling nevertheless the assigned.

--v--

Are you seeing any of the following error-traces in the Gateway node?

```javascript
ERROR - DataEndpointConnectionWorker Error while trying to connect to the endpoint. Cannot borrow client for ssl://traffic-manager-host:9711
org.wso2.carbon.databridge.agent.exception.DataEndpointAuthenticationException: Cannot borrow client for ssl://traffic-manager-host:9711
    ...
Caused by: org.wso2.carbon.databridge.agent.exception.DataEndpointException: Error while opening socket to traffic-manager-host:9711. Connection refused (Connection refused)
    ...
Caused by: java.net.ConnectException: Connection refused (Connection refused)
```

**Yes**? proceed `down`.

--v--

The mentioned errors are logged due to connectivity issues between Gateway and Traffic Manager. Gateway establishes a Binary transport communication link with Traffic Manager to publish throttle data events.

Check whether all required ports related to Binary communication (`9711` and `9611`) are opened and not blocked by any firewall. You can use the following `telnet` from Gateway instance to check the connectivity between Traffic Manager.

```shell
telnet traffic-manager-host 9611
```

--h--

## Happy Stacking!!! 👌👌👌
