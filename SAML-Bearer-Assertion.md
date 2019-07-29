---
title: SAML Bearer Assertion
intro: 'Quick Guide: SAML Bearer Assertion for OAuth 2.0'
short: A Quick Guide on SAML Bearer Assertion using WSO2 Products
category: WSO2
tags: ['saml-bearer-assertion', 'saml', 'oauth2', 'wso2']
author: Athiththan
weight: -1
updated: 2019-07-29
date: '2019-07-29T23:10:03.284Z'
---

## Brief Introduction

### SAML 2.0

SAML2 or SAML 2.0 is an XML-based framework, which is used to describe and exchange security information. SAML is commonly used for Single Sign-On (SSO) and Identity Management and Federation.

### OAuth 2.0

OAuth2 is an authorization framework used to enable applications to obtain limited access to resource owner (user) accounts.

> A quick glance and a story guide on OAuth2 framework and its grant-flows can be found here.

Both OAuth2 and SAML have similar concepts and terms in between them.

* Resource Server = Service Provider
* Authorization Server = Identity Provider
* Client

## Story Guide

Stackcheats is a private blog platform which uses **SAML SSO** to authenticate and authorize different writers and bloggers. Since Stackcheats has been a private blog platform, they have a new requirement on their development road-map to access writer's technical blogs from their  Medium accounts.

However, the Medium platform has been using **OAuth2** mechanism to authorize different users and writers to access and view various medium posts. A tech-developer named Athiththan have been requested to develop a solution.

> OAuth2 and SAML 2.0 are two different frameworks used to authorize and authenticate (OAuth doesn't authenticate).

This is where the SAML Bearer Assertion grant comes to play. The SAML Bearer Assertion grant is used to exchange the acquired SAML Assertion for an OAuth2 access token.

To simply put, SAML Bearer Assertion grant-flow is very similar to the Authorization code grant-flow in OAuth, wherein SAML Bearer Assertion flow, a SAML Assertion is used instead of a cryptography code to gain an OAuth2 access token.
