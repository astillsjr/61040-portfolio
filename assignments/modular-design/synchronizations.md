# Problem Set 2: Composing Concepts
## Exercise 2: Synchronizations for URL Shortening


**sync** generate  
**when** Request.shortenUrl (shortUrlBase)  
**then** NonceGeneration.generate (context: shortUrlBase)

**sync** register  
**when**  
Request.shortenUrl (targetUrl, shortUrlBase)  
NonceGeneration.generate (): (nonce)  
**then** UrlShortening.register (shortUrlSuffix: nonce, shortUrlBase, targetUrl)

**sync** setExpiry  
**when** UrlShortening.register (): (shortUrl)   
**then** ExpiringResource.setExpiry (resource: shortUrl, seconds: 3600)

**sync** expireResource
**when** ExpiringResource.systemExpireResource (): (resource: shortUrl)  
**then** UrlShortening.delete (shortUrl: resource)


### Question(s)

1. In the first synchronization *generate*, the `Request.shortenUrl` action only provides the `shortUrlBase` because the `NonceGeneration.generate` action only requires the context (in this case, the shortUrlBase) to generate a unique nonce (short URL suffix), so the `targetUrl` is not needed at this point. In the second synchronization *register*, both `targetUrl` and `shortUrlBase` are included because the action is now about creating the shortening, where both are necessary. 


2. In some cases, actions might involve arguments with the same name but in different contexts (e.g., `targetUrl` in the `request.shortenUrl` and `UrlShortening.register`). Using the full name makes it easier to track which argument belongs to which context.

3. The request action is included in the first two synchronizations because they both represent user-driven actions—requests to shorten URLs or register a new short URL. The *generate* synchronization is triggered by the user’s request to shorten a URL, and the *register* synchronization is triggered by the request to register the shortened URL with its target URL.The *setExpiry* synchronization does not need a request action because it’s triggered automatically when a short URL is registered.

4. If the application did not support alternative domain names and always used a fixed domain, there would be no need for `shortUrlBase` as an argument in the `request.shortenUrl` or `NonceGeneration.generate` as the base URL can be hardcoded. 

