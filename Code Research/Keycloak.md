Created at 19/03/2024 @ 09:17 by Reno Muijsenberg

# Table of Contents
[Context](#Context)
[What is Keycloak](#What%20is%20Keycloak)

# Context
For my sixth semester at Fontys, I am working on a project called 'collaborating bots'. The idea of this project is to have a network of different agents collaborate with each other to achieve a certain task. For this project we decided on using the [ActivityPub protocol](https://www.w3.org/TR/activitypub/) for communication between those agents.

When reading about how to implement this protocol, it said that there is not concrete way to implement authentication for the ActivityPub, but there are some [best practices](https://www.w3.org/wiki/SocialCG/ActivityPub/Authentication_Authorization) we could follow.

So as it states in the best practices that for client-to-server we should use the OAuth 2.0 bearer tokens, which will be provided by Keycloak.

# What is Keycloak
Keycloak is an "Open Source Identity and Access Management" application. It will allow developers to easily add authentication with minimum effort. 

# Setup
First we need to setup Keycloak so that it is up and running on or local machine, for this I will be using a docker-compose file that is already in my ActivityPub application which contains an instance of a mongodb database and the UI for this database. So, as it is going to be one project I will add the Keycloak instance to this existing docker-compose file.

Added content:
```yml
version: '3.8'  
  
services:
  keycloak:  
    image: quay.io/keycloak/keycloak:24.0.1  
    ports:  
	  - "8080:8080"  
    environment:  
	  KEYCLOAK_ADMIN: admin  
	  KEYCLOAK_ADMIN_PASSWORD: admin  
    command: start-dev

```

