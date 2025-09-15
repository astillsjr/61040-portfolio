# Problem Set 1: Reading and Writing Concepts
## Exercise 1: Reading a concept

---

**concept** GiftRegistration [User, Item]
**purpose** track purchases of requested gifts
**principle**
  a recipient creates a registry, and adds items to it indicating the number of each requested;
  opens the registry so it becomes publicly visible;
  then givers can view which items are still available and purchase them;
  and finally the recipient closes the registry, after which it is no longer publicly visible
  but the recipient can see which items were purchased and by whom.
**state**
  a set of Registrys with
    an owner User
    an active Flag
    a set of Requests

  a set of Requests with
    an Item
    a count Number
    a set of Purchases

  a set of Purchases with
    a purchaser User
    an Item
    a count Number
**actions**
  create (owner: User): (registry: Registry)
    **effects** create a new registry with this owner, active set to false and no requests

  addItem (registry: Registry, item: Item, count: Number)
    **requires** registry exists
    **effects** if a request for this item exists, add the count
    otherwise create a new request for the item with this count and add to registry

  removeItem (registry: Registry, item: Item)
    **requires** a request for this item exists in the registry
    **effects** remove the request from the registry

  open (registry: Registry)
    **requires** registry exists and it is not active
    **effects** make registry active

  close (registry: Registry)
    **requires** registry exists and is active
    **effects** make registry not active

  purchase (purchaser: User, registry: Registry, item: Item, count: Number)
    **requires** registry exists, is active and has a request for this item with at least count
    **effects** create a new purchase for this purchaser, item and count
    and decrement the count in the matching request

## Question(s)

1. The first invariant of the state is that the aggregation/count of each item in the registry (including after purchases are made),
must always be a nonnegative integer. The second invariant is that 

The first invariant is is more important because... The purchase action is most affected by the invariant, and preserves it by requiring
that each request has at least count before succeeding. This prevents the  
