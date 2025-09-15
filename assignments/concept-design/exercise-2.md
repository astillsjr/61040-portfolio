# Problem Set 1: Reading and Writing Concepts
## Exercise 2: Extending a familiar concept

---

**concept** PasswordAuthentication
**purpose** limit access to known users
**principle**
  after a user registers with a username and a password,
  they can authenticate with that same username and password
  and be treated each time as the same user
**state**
  a set of Users with 
    a username String
    a password String
    an email String
    an isConfirmed Flag
    a confirmationToken String

**actions**
  register (username: String, password: String, email: String): (user: User, confirmationToken: String)
    **requires** no existing user with this username  
    **effects** create a new user with the given username, password and email, an isConfirmed flag set to false and
    a new confrmationToken. 

  confirm (username: String, token: String)
    **requires** requires username to match an existing user with an isConfirmed flag set to false and the given token matches that user's confirmationToken
    **effects** sets the isConfirmed flag for the user associated with username to true

  authenticate (username: String, password: String): (user: User)
    **requires** a confirmed user with the given username exists and the provided password matches that user's password
    **effects** returns the authenticated user

## Question(s)

3. There may only be at most one user with a given username. This invariant is held by checking if a username is already associated
with a user before creating a new one in the register action. 