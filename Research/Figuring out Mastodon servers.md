Created at 14-03-2024 @ 13:25 by Reno Muijsenberg

First of all I want to figure out how to work with Activity Pub by kind of 'reverse engineering' the Mastodon endpoints of my own account.

I will start by the first thing I know about Activity Pub servers, which is that they should contain users profile link by making an HTTP POST to:
`/.well-known/webfinger?resource=acct:Reno@social.edu.nl`
As I have my Fontys account linked to the `social.edu.nl`Mastodon server.

I will make go to the following URL in the browser:
https://social.edu.nl/.well-known/webfinger?resource=acct:Reno@social.edu.nl


The output of this URL is:
```json
{
  "subject": "acct:Reno@social.edu.nl",
  "aliases": [
    "https://social.edu.nl/@Reno",
    "https://social.edu.nl/users/Reno"
  ],
  "links": [
    {
      "rel": "http://webfinger.net/rel/profile-page",
      "type": "text/html",
      "href": "https://social.edu.nl/@Reno"
    },
    {
      "rel": "self",
      "type": "application/activity+json",
      "href": "https://social.edu.nl/users/Reno"
    },
    {
      "rel": "http://ostatus.org/schema/1.0/subscribe",
      "template": "https://social.edu.nl/authorize_interaction?uri={uri}"
    }
  ]
}
```

In this JSON we can see a couple of links, if we simply click on these links we will be redirected to my own profile page of the Mastodon server. Which is not the desired result as I was hoping on getting more information on my own user account for example where the inbox and outbox are located.

So, what should I try next? Well, I will first try using Postman to get the contents of my user account. For this I will make a HTTP GET the following URL:
https://social.edu.nl/users/Reno

But, this also returns the HTML only in Postman, The next step I will try is changing the Accept header of the request to `application/activity+json`. And after this make the call again.

With this time a successful operation that returns the JSON:
```json
{
    "@context": [
        "https://www.w3.org/ns/activitystreams",
        "https://w3id.org/security/v1",
        {
            "manuallyApprovesFollowers":"as:manuallyApprovesFollowers",
            "toot": "http://joinmastodon.org/ns#",
            "featured": {
                "@id": "toot:featured",
                "@type": "@id"
            },
            "featuredTags": {
                "@id": "toot:featuredTags",
                "@type": "@id"
            },
            "alsoKnownAs": {
                "@id": "as:alsoKnownAs",
                "@type": "@id"
            },
            "movedTo": {
                "@id": "as:movedTo",
                "@type": "@id"
            },
            "schema": "http://schema.org#",
            "PropertyValue": "schema:PropertyValue",
            "value": "schema:value",
            "discoverable": "toot:discoverable",
            "Device": "toot:Device",
            "Ed25519Signature": "toot:Ed25519Signature",
            "Ed25519Key": "toot:Ed25519Key",
            "Curve25519Key": "toot:Curve25519Key",
            "EncryptedMessage": "toot:EncryptedMessage",
            "publicKeyBase64": "toot:publicKeyBase64",
            "deviceId": "toot:deviceId",
            "claim": {
                "@type": "@id",
                "@id": "toot:claim"
            },
            "fingerprintKey": {
                "@type": "@id",
                "@id": "toot:fingerprintKey"
            },
            "identityKey": {
                "@type": "@id",
                "@id": "toot:identityKey"
            },
            "devices": {
                "@type": "@id",
                "@id": "toot:devices"
            },
            "messageFranking": "toot:messageFranking",
            "messageType": "toot:messageType",
            "cipherText": "toot:cipherText",
            "suspended": "toot:suspended",
            "memorial": "toot:memorial",
            "indexable": "toot:indexable"
        }
    ],
    "id": "https://social.edu.nl/users/Reno",
    "type": "Person",
    "following": "https://social.edu.nl/users/Reno/following",
    "followers": "https://social.edu.nl/users/Reno/followers",
    "inbox": "https://social.edu.nl/users/Reno/inbox",
    "outbox": "https://social.edu.nl/users/Reno/outbox",
    "featured":"https://social.edu.nl/users/Reno/collections/featured",
    "featuredTags":"https://social.edu.nl/users/Reno/collections/tags",
    "preferredUsername": "Reno",
    "name": "Muijsenberg,Reno R.F.",
    "summary": "",
    "url": "https://social.edu.nl/@Reno",
    "manuallyApprovesFollowers": false,
    "discoverable": false,
    "indexable": false,
    "published": "2024-03-07T00:00:00Z",
    "memorial": false,
    "devices": "https://social.edu.nl/users/Reno/collections/devices",
    "publicKey": {
        "id": "https://social.edu.nl/users/Reno#main-key",
        "owner": "https://social.edu.nl/users/Reno",
        "publicKeyPem": "-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzA4Ho30x+6tudnHyEvWp\n8E5uWgDMU6yYQ5BFxNhWZ8+I1BJ/Uedt7NTtzouo0NaLj8SAgGZsFLeg3emx9fLx\nJ2QzxUiIiHGwvKlLFZzTPYxYo9p1AsAYQblO9aPLcwD8XTTdyjCa+1UXl+fQSiU6\nEIrfBx6L6m6JmCTAGSks7MVuZd0csj9wJbyVn/gEzSQ2zVteghC3HMJ6dHNbOBZG\nILjO13Xbr09oGX0Y53fkgeEW4wwrO3a83NHTmBlJVFsA3Jo5b0Vyc26h6ZL39br1\nQE2yyw3AQguXmLEoobYaf1XQeMlkUSArnajF70XnGNQZzyrqACjUOWltOPfPpJm2\nEQIDAQAB\n-----END PUBLIC KEY-----\n"
    },
    "tag": [],
    "attachment": [],
    "endpoints": {
        "sharedInbox": "https://social.edu.nl/inbox"
    }
}
```

So in this long JSON are some important things I want to note for myself:
- Inbox: https://social.edu.nl/users/Reno/inbox
- Outbox: https://social.edu.nl/users/Reno/outbox
- Public Key:
	- id: https://social.edu.nl/users/Reno#main-key
	- owner: https://social.edu.nl/users/Reno
	- Public Key Pem: (big key to type)

Server POST naar mijn inbox.
