# Problem Set 2: Composing Concepts
## Exercise 3: Extending the Design


**concept** ShortUrlAnalytics [User]  
**purpose** tracks the number of accesses for each shortened URL  
**principle** each time a short URL is accessed (i.e., looked up), the system increments the access count for that URL.   
Only the user who created the shortening can view the analytics for that short URL.  

**state**
  - a set of ShortUrlAnalytics with
    - a shortUrl String
    - an accesses Number
    - a creator User

**actions**
  - `create (shortUrl: String, creator: User)`
    - **effect**: create an analytic for shortUrl accesses initialized at 0

  - `increment (shortUrl: String)`
    - **effect**: increments the accesses count for the short Url

  - `view (shortUrl: String, user: User): (accesses: Number)`
    - **requires**: user matches the creator of the short Url
    - **effect**: returns the number of accesses to the short Url

  - `delete (shortUrl: String)`
    - **requires**: a shortURLAnalytic exists for shortUrl 
    - **effect**: deletes the shortURLAnalytic associated with shortUrl

**sync** createAnalyticsForShortening  
**when** UrlShortening.register (shortUrlSuffix, shortUrlBase, targetUrl): (shortUrl)  
**then** ShortUrlAnalytics.create (shortUrl, creator: User)  

**sync** incrementAnalyticsOnLookup  
**when** UrlShortening.lookup (shortUrl): (targetUrl)  
**then** ShortUrlAnalytics.increment (shortUrl)  

**sync** viewAnalytics  
**when** Request.viewAnalytics (shortUrl, User)   
**then** ShortUrlAnalytics.view(shortUrl, User): (accesses)

### Features

**Allowing Users to Choose Their Own Short URLs:**  
This is possible by modifying the UrlShortening.register action to allow custom short URL suffixes and ensuring that the ShortUrlAnalytics is created with the user-specified suffix.

**Using the “Word as Nonce” Strategy:**  
We would modify the NonceGeneration concept to support generating short URLs based on words from a predefined dictionary. This can work alongside the analytics concept without requiring changes to the ShortUrlAnalytics concept.

**Including Target URL in Analytics:**  
The ShortUrlAnalytics concept could store the targetUrl as an additional field, but this may compromise privacy since different users can make short URLs to the same target Url, and analytics lookups should only be in the context of a single user at a time. 

**Generate Short URLs That Are Not Easily Guessed:**  
We could modify NonceGeneration to generate cryptographically secure random nonces, which would make guessing short URLs more difficult.

**Supporting Reporting of Analytics to Non-Registered Users:**  
This would violate the privacy principle of keeping analytics restricted to the creator. Therefore, this feature should not be included in the design.