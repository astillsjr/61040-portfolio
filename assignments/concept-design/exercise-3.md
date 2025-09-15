# Problem Set 1: Reading and Writing Concepts
## Exercise 3: Comparing concepts

---

**concept** PersonalAccessToken[User, Scope]
**purpose** authenticate users securely without using a password 
**principle**
  a user creates a token, which has the capabilites to access resources and perform actions 
  on those resources that the owner of the token has;
  addtional scopes and permissions can be granted to the token to further limit what it can
  access;
**state**
  a set of Users with 
    a set of Tokens
  a set of Tokens with
    a name String
    an expiration Date (optional)
    a set of Scopes
    an active Flag
**actions**
  generateToken (user: User, name: String, expiration: Date = None, scopes: Set<Scope>): (token: Token)
    **requires** no existing token with the given name
    **effects** create a new active token with the given name for the specified user, 
    associated with the specified scopes and optional expiration

  deleteToken (user: User, token: Token)
    **requires** the provided token already exists
    **effects** removes the given token from the set of tokens associated with the 
    given user

  revokeToken (user: User, token: Token)
    **requires** the provided token already exists and is active
    **effects** sets the active flag of the given token to false

  regnerateToken (token: Token, name: string, expiration: Date = None): (token: Token)
    **requires** the provided token already exists
    **effects** creates a new token with the given name and optional expiration that maintains
    the permissions of the given token

  useToken (username: String, token: String): (resource: Resource)
    **requires** an active token with appropriate scopes for the requested action
    **effects** authenticate the user based on the token, granting access to 
    GitHub resources according to the token's permissions

## Response

*PersonalAccessToken* differs from *PasswordAuthentication* because they can be scoped to specific actions or resources, limiting the risk if they are compromised. A password grants full access to the account, while a personal access token can be limited in scope and can be revoked independently without affecting the user’s entire account. I would change the GitHub documentation by providing concrete examples of when to use a personal access token (e.g., automating tasks via the API, performing Git operations on the command line) and contrast this with the user login flow where passwords are still necessary.

