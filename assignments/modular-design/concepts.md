# Problem Set 2: Composing Concepts
## Exercise 1: Concepts for URL Shortening


**concept** UrlShortening  
**purpose** shorter or more memorable way to link  
**principle** after registering a short url, lookup will return the original url  

**state**
  - a set of Shortenings with
    - a targetUrl String
    - a shortUrl String

**actions**
  - `register (shortUrlSuffix, shortUrlBase, targetUrl: String): (shortUrl: String)`
    - **requires**: no shortening exists for shortUrlBase/shortUrlSuffix
    - **effect**: forms and saves a new short URL by appending suffix to base and associates it with targetUrl

  - `lookup (shortUrl: String): (targetUrl: String)`
    - **requires**: a shortening exists for shortUrl
    - **effect**: returns the target URL associated with a short URL

  - `delete (shortUrl: String)`
    - **requires**: a shortening exists for shortUrl
    - **effect**: deletes a short URL and its association with a target URL

**concept** NonceGeneration [Context]  
**purpose** generate unique strings within a context  
**principle** each generate returns a string not returned before for that context  

**state**
  - a set of Contexts with
    - a used set of Strings

**actions**
  - `generate (context: Context) : (nonce: String)`
    - **effect**: returns a nonce that is not already used by this context

**concept** ExpiringResource [Resource]  
**purpose** expire resources automatically to manage costs  
**principle** after setting expiry for a resource, the system will expire it after the given time  

**state**
  - a set of Resources with
    - an expiry DateTime

**actions**
  - `setExpiry (resource: Resource, seconds: Number)`
    - **effect**: associates an expiry seconds from now with resource

  - `system expireResource () : (resource: Resource)`
    - **requires**: expiry for some resource is in the past
    - **effect**: returns the resource, and removes it from the set

Note: The expireResource action of ExpiringResource is a system action, performed spontaneously when its precondition is true (namely when the expiry time for some resource, which it returns, has arrived).

### Question(s)

1. The contexts in the `NonceGeneration` are used to organize and track unique nonces within differnet operational enviroments or groups. In the URL shortening app, a context would be each shortURLBase, that way when a suffix nonce is generated for a specific base, the resulting shortUrlBase/shortUrlSuffix is guarenteed to be unique. 

2. The `NonceGeneration` concept needs to store sets of used strings to verify that the nonces it generates within its context are unique and haven't been previously used. If a simple counter is used instead of a set, the counter will generate a new number every time the generate action is called. This number can then be converted into a string and used as the nonce. The set of used strings in the specification can be thought of as the mechanism for tracking the integers that have already been converted into nonces.

3. One advantage of using common dictionary words for nonce generation is that users find it easier to remember short URL suffixes that are words, especially if they are meaningful or related to the content. A significant disadvantage is that the number of dictionary words is limited. If the dictionary is not sufficiently large, the system may run into problems with collisions, particularly in cases where many users are generating short URLs. To modify the `NonceGeneration` concept to use dictionary words instead of random nonces, the system would need to incorporate a dictionary of words (either finite or dynamically loaded from an external dictionary source). When a nonce is generated, it attempts to generate a randomly selected a word from the dictionary. 