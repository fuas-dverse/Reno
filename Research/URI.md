Created at: 27-02-2024 @ 09:55 by Reno Muijsenberg

# Table of Contents
[Context](#Context)
[What is an URI](#What%20is%20an%20URI)
[Types of URI](##Types%20of%20URI)
[What is an URN](###What%20is%20an%20URN)
[What is an URL](###What%20is%20an%20URL)

# Context
For our project 'Collaborating Bots' we most likely will be making use of the [[activity pub]] communication protocol. The activity pub uses [[JSON-LD]] to send data from one to another. JSON-LD make use of URI to structure their data types. For using this it will be nice to know the difference between URL and URI.

# What is an URI
The abbreviation URI stand for `Uniform Resource Identifier`. It is an unique sequence of characters that identifies an abstract or physical resource, such as a webpage, email address, phone number, real-world objects such as people etc.

For example, `foo://example.com:8042/over/there?name=ferret#nose` is a URI containing a scheme name, authority, path, query and fragment. A URI does not need to contain all these components. All it needs is a [scheme name and a file path](https://tools.ietf.org/html/rfc3986#section-3), which can be empty.

A couple of URI schemes are:
 - [ftp://ftp.is.co.za/rfc/rfc1808.txt](ftp://ftp.is.co.za/rfc/rfc1808.txt)
 - [http://www.ietf.org/rfc/rfc2396.txt](http://www.ietf.org/rfc/rfc2396.txt)
 - ldap://[2001:db8::7]/c=GB?objectClass?one
 - mailto:John.Doe@example.com
 - news:comp.infosystems.www.servers.unix
 - tel:+1-816-555-1212
 - telnet://192.0.2.16:80/
 - urn:oasis:names:specification:docbook:dtd:xml:4.1.2

Source(s):
- https://en.wikipedia.org/wiki/Uniform_Resource_Identifier
- https://blog.hubspot.com/website/uri-vs-url
- https://datatracker.ietf.org/doc/html/rfc3986#section-1.1.2

## Types of URI
There are two types of URIs: URNs and URLs.

### What is an URN
A URN is an `Uniform Resource Name` which is a persistent and location-independent identifier that follows the 'URN' scheme. In this context persistent means that the URN persists in identifying the same resource over time.

The main purpose of a URN is to serve as a long-lasting identifier for a resource, ensuring that the identifier remains valid over time.

Source(s):
- https://medium.com/@abhirup.acharya009/uri-vs-urn-vs-url-key-distinctions-explained-dec8e02ebd18
- https://en.wikipedia.org/wiki/Uniform_Resource_Name

### What is an URL
The abbreviation URL stand for `Uniform Resource Locators`. With hypertext and HTTP an URL is one of the key concepts of the web. It is the mechanism by browsers to retrieve any published resource on the web.

An URL is nothing more that the address of a given resource on the web. Each valid URL should point to a unique resource on the web. A resource can be a HTML page, a CSS document, an image etc.  

A URL consist of a couple of different part, some of those parts are mandatory and some of those parts are optional. Take for example the following URL:

`https://www.example.com:80/path/to/myfile.html?key1=hello#test`

When dissecting this URL we can get the following parts:
- http / https = scheme
- www.example.com = domain name
- 80 = port
- /path/to/myfile.html = path to file
- ?key1=hello = parameters
- `#`test = place in document

Source(s):
- https://developer.mozilla.org/en-US/docs/Learn/Common_questions/Web_mechanics/What_is_a_URL
- https://en.wikipedia.org/wiki/URL