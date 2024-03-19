Created at 14-03-2024 @ 09:35 by Reno Muijsenberg

# Activity Vocabulary
[The Activity Streams 2.0 Core Syntax](https://www.w3.org/TR/activitystreams-core/) = JSON syntax for Activity Streams

Base URI: `https://www.w3.org/ns/activitystreams#`.

## Core types
The Activity Vocabulary Core Types provide the basis for the rest of the vocabulary.

Describes an object of any kind, this object serves as the base type for most of the other objects.

Core types:
- [Core Types](https://www.w3.org/TR/activitystreams-vocabulary/#types)

## Extended Types
The Activity Streams 2.0 Extended Types include Activity and Object subtypes that are common to many social Web applications.

Extended types:
- [Activity Types](https://www.w3.org/TR/activitystreams-vocabulary/#activity-types) = Extends from base `Activity Type`.
- [Actor Types](https://www.w3.org/TR/activitystreams-vocabulary/#actor-types) = `Object Type` capable of performing activities.
- [Object Types](https://www.w3.org/TR/activitystreams-vocabulary/#object-types) = Extends from base `Object Type`.

### Activity Types
Also known as things users can do on social networks. Includes things like: `Accept`, `Add`, `Block`, `Create`, `Delete` etc.

> An **Activity** is something _that can be done._

### Actor Types
An `Actor` is some sort of user, this can be a `person`, `bot`, `agent`, `company`, `group of people` and more. 

> An **Actor** is the _thing that is doing_ an **Activity**.

### Object Types
An `Objects` are the things that get sent around in a network, for example a `Note`, `Article`, `Image`, `Video` etc.

> An **Object** will be create by an **Person** doing an **Activity**

## Properties
Properties are some additional things to specify in a given JSON, for example: `id`, `type`, `actor`, `attachment` etc.

Properties:
- [Properties](https://www.w3.org/TR/activitystreams-vocabulary/#properties)

Source(s):
- https://tinysubversions.com/notes/reading-activitypub/
- https://www.w3.org/TR/activitystreams-vocabulary/

# ActivityPub
[[Activity Pub]]
