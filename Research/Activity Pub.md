Created at: 20-02-2024 @ 13:13 by Reno Muijsenberg

# Table of Contents
[Context](#Context)
[What is the Activity Pub](#What%20is%20the%20Activity%20Pub)
[How does the Activity Pub work](##How%20does%20the%20Activity%20Pub%20work)
[Actors](###Actors)
[ActivityStreams](###ActivityStreams)
[Example](###Example)

# Context
As the subject of decentralization is fairly new to our group, the teacher gave us some technologies to look one of the being the activity pub. And as this subject seems very interesting to me I am going to write this research on the activity pub is and how to use it.

# What is the Activity Pub
The ActivityPub protocol is a decentralized social networking protocol based on the ActivityStreams 2.0 data format and JSON-LD. It is equipped with a client to server API for creating, updating and deleting content, as well as a federated server to server API for delivering notifications and content.

At an early stage, the name of this protocol was "ActivityPump" but it was later renamed to "ActivityPub" as it was a better indicated of the cross-publishing purpose of the protocol.

In January of 2018 the World Wide Web Consortium (W3C) published the ActivityPub standards as a recommendation.

Source(s)
* https://www.w3.org/TR/activitypub/
* https://en.wikipedia.org/wiki/ActivityPub

## How does the Activity Pub work
The ActivityPub has two layers:
* 1) - **A server to server federation protocol** (so decentralized websites can share information)
* 2) - **A client to server protocol** (so users can communicate with the ActivityPub using their accounts)

It is possible to implement these two layers separately. however if you have implemented one of these two layer it is not that many extra steps the implement the other, and this ensures that you get all the benefits of decentralization.

So the following actions can happen when using the ActivityPub:
* POST to someone's inbox (server-to-server)
* GET from your own inbox (client-to-server)
* POST to the outbox you sent a message to (client-to-server)
* GET from an outbox to see the posted messages (client-to-server and/or server-to-server)

### Actors
In the ActivityPub an "user" is represented as an "actor" via the user's account on a server. User accounts on different servers correspond to different actors. Every actor has an inbox and an outbox.
* The inbox: how they get message from the rest of the world.
* The outbox: how they send message to the rest the others. 

These inboxes and outboxes are just regular URL endpoints which are listed in the ActivityPub actor's ActivityStreams description.

### ActivityStreams
ActivityPub uses ActivityStreams for its vocabulary, these ActivityStreams includes all the common terms you need to represent all the activities and content flowing around a social network. If it does not include something in its vocabulary then it can be extended via JSON-LD.

### Example
In this part I will create a small example on how this will work in practice.

First to send a message create an ActivityStream object that will be posted to your own outbox.
```json-ld
{
	"@context": "https://www.w3.org/ns/activitystreams",
	"type": "Note",
	"to": ["https://otherServer.com/otherUser/"],
	"attributedTo": "https://myServer.com/myUser/",
	"content": "Hi, I am testing to contact you"
}
```
So far this is a non-activity object, the server recognizes that this is an object being newly created, and will wrap this in a create activity.


```json-ld
{
	"@context": "https://www.w3.org/ns/activitystreams",
	"type": "Create",
	"id": "https://myServer.com/myUser/posts/a29a6843-9feb-4c74-a7f7-081b9c9201d3",
	"to": ["https://otherServer.com/otherUser/"],
	"actor": "https://myServer.com/myUser/",
	"object": {
		"type": "Note",
		"to": ["https://otherServer.com/otherUser/"],
		"attributedTo": "https://myServer.com/myUser/",
		"content": "Hi, I am testing to contact you"
	}
}
```

My server will look up the other server's ActivityStreams actor object, then finds his inbox endpoint and POSTs the object to his inbox. Doing this involves two steps, first a client-to-server and secondly a server-to-server communication.
