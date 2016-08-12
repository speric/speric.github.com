---
layout: post
title: "Understanding updateIn & keyPath in ImmutableJS"
header_img: "dusk.jpg"
description: "Understanding keyPath in ImmutableJS"
hn: https://news.ycombinator.com/item?id=12277037
---
[ImmutableJS](https://facebook.github.io/immutable-js/) is
a neat library, especially when used in combination with [Redux](https://github.com/reactjs/redux). I'm not ashamed to
admit, though, that I found the docs confusing at first. For example,
look at the method signature for `Map#updateIn()`:
<br/><br/>


<center><div><img src="/assets/images/keypath.png" class="img-polaroid"/></div></center>

<br/>
`updateIn()` is a useful function to use when you're interacting with Redux. I had to understand it.  In the end, what helped me become productive with `updateIn()` was understanding the `keyPath` parameter.

The key (sorry) to understanding `keyPath` is to realize it's just an
array of values that Immutable will call `get` with as it works it's way through whatever data
structure you're calling `updateIn()` on. It doesn't matter if the value is a `Map` key or a `List` index. This works because both `Map#get`
and `List#get` inherit from `Iterable#get`
([https://facebook.github.io/immutable-js/docs/#/Iterable/get](https://facebook.github.io/immutable-js/docs/#/Iterable/get)).

Consider the following data structure, which is a `Map` of `endpoints`, keyed on their `id`. The values are a `List` of `Maps` that represent that endpoint's "subscriptions" to a
given event (`enabled: true|false`):


```
const endpoints = fromJS({
  "abc-123": [
    {
      id: 111,
      endpoint_id: "abc-123",
      event_key: "signup_success",
      enabled: true,
    },
    {
      id: 222,
      endpoint_id: "abc-123",
      event_key: "signup_failure",
      enabled: false,
    },
  ],
  "def-456": [
    {
      id: 333,
      endpoint_id: "def-456",
      event_key: "signup_success",
      enabled: false,
    },
    {
      id: 444,
      endpoint_id: "def-456",
      event_key: "signup_failure",
      enabled: true,
    },
  ],
});
```
<br/>
To get all the subscriptions for a given endpoint, we use `Map#get` as
follows:

```javascript
subscriptions = endpoints.get("abc-123");
keyPath = ["abc-123"]
```

Since `subscriptions` is now a `List`, we can get the first element using `List#get`, passing it the index of the element we want to access:

```javascript
subscription = subscriptions.get(0);
keyPath = [0]
```

We could do this in one shot:

```javascript
subscription = endpoints.get("abc-123").get(0);
keyPath = ["abc-123", 0]
```

What's neat is that we can construct a `keyPath` down to an attribute of
an element. Say we wanted to get the `enabled` attribute of the above
endpoint:

```javascript
enabled = endpoints.get("abc-123").get(0).get('enabled');
keyPath = ["abc-123", 0, "enabled"]
```

Now that we know how to construct a `keyPath` to any point in a deeply
nested data structure, we can move to understanding the `updater` parameter of `updateIn`.

The simplest way to understand `updater` is that it's a function
that will be passed as it's parameter the result of calling `get` on the
final element in the `keyPath` array. It should return the new value for
that...value. It's easier to show in code.

Let's say I wanted to flip the `enabled` flag for the subscription
represented by element `0` for endpoint `abc-123`:

```javascript
endpoints.updateIn(["abc-123", 0, 'enabled'], isEnabled => !isEnabled);
```

In the above example, I constructed the `keyPath` right down to the
`enabled` flag (the first parameter), and the second parameter is a
function that will be passed the current value of `enabled` as a parameter called `isEnabled`, and will simply return opposite boolean value of `isEnabled`.

You could back the `keyPath` up one level, as `["abc-123", 0]`, which
would give you the entire subscription `Map` at that index, and then
return a new `Map` with the `enabled` flag flipped. But in my
(limited) experience, I've not updated more than one attribute of a
given object at a time in a reducer. Hope this helps!
