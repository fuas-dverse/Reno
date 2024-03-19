Created at 19-03-2024 @ 10:39 by Reno Muijsenberg

Did you know that a single protocol, [ActivityPub](https://fuas-dverse.github.io/concepts/activitypub/), is the backbone of the entire Fediverse? This powerful tool allows data to flow freely between Mastodon servers, fostering a truly interconnected social web. Today, we'll explore ActivityPub and show you how to use it to retrieve your Mastodon data.

In this article I will try to get a better understanding of the ActivityPub protocol by 'reverse engineering' and figuring out what data we can retrieve from the [Social Edu Mastodon servers](https://social.edu.nl/).

# Key ActivityPub Endpoints
After having delved into the world of the ActivityPub. There are a few endpoints that we are going to explore.

> [!note] Variable syntax
> For the remainder of this article I will use the syntax {value} as variables, where value is replaced with the expected information.

## WebFinger
This endpoint utilizes the [WebFinger protocol](https://en.wikipedia.org/wiki/WebFinger) to discover the ActivityPub actor associated with a specific username and domain combination.
- Endpoint: `{base_url}/.well-know/webfinger?resource=acct:{username}@{domain}`

## User
This endpoint retrieves information about a particular user on the ActivityPub server.
- Endpoint: ``{base_url}/users/{username}

## Outbox
The outbox endpoint acts as the repository for ActivityStreams objects created by the user.
- Endpoint: `{base_url}/users/{username}/outbox

## Inbox
The inbox endpoint serves as the designated location for receiving ActivityStreams objects directed towards the user.
- Endpoint: `{base_url}/users/{username}/inbox`

# Retrieving Information
In this section we will try and retrieve or post data to the [Key ActivityPub Endpoints](#Key%20ActivityPub%20Endpoints) specified in the previous section.

## WebFinger
First we will start by making a HTTP-GET request to the WebFinger endpoint as this should contain the endpoint link to our user account. This endpoint can simply be opened in the browser to view the information.

Input:

| Endpoint | https://social.edu.nl/.well-known/webfinger?resource=acct:Reno@social.edu.nl |
| -------- | ---------------------------------------------------------------------------- |
| Method   | GET                                                                          |
| Client   | Brave Browser                                                                |
| Headers  | -                                                                            |

Output:
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

## User
The returned JSON response in previous section contains links to the user's Mastodon profile. First we will try to get the information via the browser.

Input:

| Endpoint | https://social.edu.nl/users/Reno |
| -------- | -------------------------------- |
| Method   | GET                              |
| Client   | Brave Browser                    |
| Headers  | -                                |
Output:
- Social.edu Mastodon profile page.

While visiting these links redirects us to the users profile, it does not provided the desired information regarding the users account, such as the endpoints to the inbox, outbox etc.

The next step to try is to I will try is to make a request to the provided endpoints using the Postman client, in this client we will make a HTTP-GET request to:

Input:

| Endpoint | https://social.edu.nl/users/Reno |
| -------- | -------------------------------- |
| Method   | GET                              |
| Client   | Postman                          |
| Headers  | -                                |
Output:
- Source code of Social.edu Mastodon profile page.


The next step I will try is to change the `Accept` header of the request to `application/activity+json`. To change this header, navigate to the `Headers` tab in the Postman client and add a new header.
- `Key`: `Accept`
- `Value`: `application/activity+json`

Input:

| Endpoint | https://social.edu.nl/users/Reno  |
| -------- | --------------------------------- |
| Method   | GET                               |
| Client   | Postman                           |
| Headers  | Accept: application/activity+json |
Output:
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

In this big JSON there are a couple of things to note down, first of all are the two endpoints for the inbox and outbox.
- Inbox: https://social.edu.nl/users/Reno/inbox
- Outbox: https://social.edu.nl/users/Reno/outbox

Then there is an object that contains the information of the public keys, used to sign requests. 
- Id: https://social.edu.nl/users/Reno#main-key
- Owner: https://social.edu.nl/users/Reno
- PublicKeyPem: -----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A`{rest of key}`\nEQIDAQAB\n-----END PUBLIC KEY-----\n
## Outbox
Now that we retrieved the inbox and outbox from the user, we will try and read the outbox using the browser, as this is the most simple way to GET the information.

Input:

| Endpoint | https://social.edu.nl/users/Reno/outbox |
| -------- | --------------------------------------- |
| Method   | GET                                     |
| Client   | Brave Browser                           |
| Headers  | -                                       |
Output:
```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "https://social.edu.nl/users/Reno/outbox",
  "type": "OrderedCollection",
  "totalItems": 0,
  "first": "https://social.edu.nl/users/Reno/outbox?page=true",
  "last": "https://social.edu.nl/users/Reno/outbox?min_id=0&page=true"
}
```

As we can see this JSON returns the data belonging the my outbox, the data is being structured with an [OrderedCollection](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-orderedcollection), as should  be the case stated in the ActivityPub specs.

> [!note] ActivityPub Specs
> The `outbox` _MUST_ be an [`OrderedCollection`](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-orderedcollection).

The strange thing about this is that it the `totalItems` is 0, because I did send and receive a friend requests. So, it properly is partially protected by some kind of authentication.

>[!Note]
>Even after syncing the Mastodon cookies (\_mastodon_session, \_session_id, SimpleSAML and SimpleSAMLAuthToken) with my local Postman. Its still only showed an empty outbox.
## Inbox
The last thing to try and do, is to POST something to our own inbox. To do this I will use the Postman client, and use a body from the original ActivityPub specs but only change the from and to, to my own account.

Input:

| Endpoint | https://social.edu.nl/users/Reno/inbox                                                                                                                                                                                                                                                                       |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Method   | POST                                                                                                                                                                                                                                                                                                         |
| Client   | Postman                                                                                                                                                                                                                                                                                                      |
| Headers  | Content-type: application/ld+json; profile="https://www.w3.org/ns/activitystreams"                                                                                                                                                                                                                           |
| Body     | <pre>{<br>    "@context": "https://www.w3.org/ns/activitystreams",<br>    "type": "Note",<br>    "to": [<br>        "https://social.edu.nl/users/Reno"<br>    ],<br>    "attributedTo": "https://social.edu.nl/users/Reno",<br>    "content": "Say, did you finish reading that book I lent you?"<br>}</pre> |

Output:
```json
{
    "error": "Request not signed"
}
```

> [!Warning] Update
> After reading the Mastodon documentation, I figured out that to POST something to someone's inbox endpoint, we need to sign the request.