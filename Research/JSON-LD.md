Created at: 21-02-2024 @ 16:05 by Reno Muijsenberg

# Table of Contents
[Context](#Context)
[What is JSON](#What%20is%20JSON)
[What is JSON-LD](#What%20is%20JSON-LD)
[What are the differences](#What%20are%20the%20differences)

# Context
As the subject of decentralization is fairly new to our group, the teacher gave us some technologies to look one of the being the activity pub, when looking into this protocol we came to the conclusion that JSON-LD is a big concept within this protocol. That is the reason why I want to figure out in this document what exactly JSON-LD is.

# What is JSON
First of all we need to know what regular JSON is. JSON stands for JavaScript Object Notation it is a lightweight format for storing and transporting data. JSON is often used when data is being sent from a server to a webpage. 

JSON is structured in the following way:
- Data is in name/value pairs
- Data is separated by commas
- Curly braces hold objects
- Square brackets hold arrays

An example of a person in JSON:
```JSON
{
  "name": "John Lennon",
  "born": "1940-10-09",
  "spouse": "Cynthia Lennon"
}
```

Source(s)
* https://www.w3schools.com/whatis/whatis_json.asp
* https://en.wikipedia.org/wiki/JSON

# What is JSON-LD
JSON-LD stands for JavaScript Object Notation for Linked Data, it is a method of encoding linked data using JSON. JSON-LD's goal is to make JSON documents understandable by machines by linking it to well defined vocabularies. By using these defined vocabularies the machine can not only read the JSON but also understand it.

An example of a person in JSON-LD is:
```JSON-LD
{
  "@context": "https://json-ld.org/contexts/person.jsonld"
  "@id": "http://dbpedia.org/resource/John_Lennon",
  "name": "John Lennon",
  "born": "1940-10-09",
  "spouse": "http://dbpedia.org/resource/Cynthia_Lennon"
}
```

At the top of this JSON there is the @context field, this field refers to a place where someone can look up more about what the values below it mean.

When talking about decentralization JSON-LD is especially useful as the types are not defined locally in our own code bases. 

Source(s)
* https://en.wikipedia.org/wiki/JSON-LD
* https://json-ld.org/

# What are the differences
So what exactly is the difference between JSON and JSON-LD and what are some specific use cases for these two types.

First of all the key difference between JSON and JSON-LD is that JSON is readable by machines but not understandable, which is when using JSON-LD.

JSON-LD allows issuers to define a type of credential that they are issuing and then use the @context for navigating a tree of a scheme that explains the structure and content of a credential. This helps the verifier to confirm the meaning of all attributes of the JSON.

Source(s):
- https://stackoverflow.com/questions/40136619/what-are-the-differences-between-json-ld-and-json-schema