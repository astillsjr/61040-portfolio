# Problem Set 1: Reading and Writing Concepts
## Exercise 1: Reading a concept

---

**concept** GiftRegistration [User, Item]  
**purpose** track purchases of requested gifts

**principle**
  - a recipient creates a registry, and adds items to it indicating the number of each requested;
  - opens the registry so it becomes publicly visible;
  - then givers can view which items are still available and purchase them;
  - and finally the recipient closes the registry, after which it is no longer publicly visible, but the recipient can see which items were purchased and by whom.

**state**
  - a set of Registrys with:
    - an owner User
    - an active Flag
    - a set of Requests

  - a set of Requests with:
    - an Item
    - a count Number
    - a set of Purchases

  - a set of Purchases with:
    - a purchaser User
    - an Item
    - a count Number

**actions**
  - `create (owner: User): (registry: Registry)`
    - **effects**: create a new registry with this owner, active set to false and no requests.

  - `addItem (registry: Registry, item: Item, count: Number)`
    - **requires**: registry exists.
    - **effects**: if a request for this item exists, add the count; otherwise create a new request for the item with this count and add to registry.

  - `removeItem (registry: Registry, item: Item)`
    - **requires**: a request for this item exists in the registry.
    - **effects**: remove the request from the registry.

  - `open (registry: Registry)`
    - **requires**: registry exists and it is not active.
    - **effects**: make registry active.

  - `close (registry: Registry)`
    - **requires**: registry exists and is active.
    - **effects**: make registry not active.

  - `purchase (purchaser: User, registry: Registry, item: Item, count: Number)`
    - **requires**: registry exists, is active and has a request for this item with at least count.
    - **effects**: create a new purchase for this purchaser, item, and count and decrement the count in the matching request.

## Question(s)

1. The first invariant of the state is that the aggregation/count of each item in the registry (including after purchases are made) must always be a nonnegative integer. The second invariant is that the count of purchases made for each item should never exceed the number of items requested. The first invariant (non-negative item count) is more important because it directly impacts the ability to track the state of the registry in a consistent manner. A negative item count would break the entire tracking system, as the logic around available items depends on this invariant. The purchase action is most affected by this invariant. If the item count drops below zero during a purchase, it would lead to inconsistencies. This invariant is preserved by checking that the purchase count is never more than the available count of the item in the registry.

2. The purchase action potentially violates the first invariant. If more items are purchased than the available count, this would result in negative numbers in the requests. This might happen if two users are purchasing the same item simultaneously or if there's a failure in concurrency management, making it possible for the count to drop below zero.
