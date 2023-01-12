# Derailed Gateway v1

After much conception and two versions, the Derailed Gateway will finally be heading for its final phase like it's brother, the API, before it. This will be bringing massive stability improvements, code improvements, and much more since we have learnt so much with elixir over the past month.

For me, learning it was such an improvement for my coding patterns as it helped me learn the fundamentals of functional programming and step into the world of Rust.

## System Design

The Gateway Design is, and always has been, complicated to allow for higher scalability. And since it's in Elixir, also quite different from how many other alternatives do it.

In a theorem, it's quite simple, but in practice it's advanced because of the many block points and required speed ups to run fast.

When any action happens which requires the Gateway to dispatch an event, it's sent either through one of our gRPC services for the Gateway or into the Gateway itself.

This process is quite simple, just tracking down the process of the specified Guild or User then publishing a message to that process.

One thing though which I haven't talked about yet is subscribing. When a user subscribes to a Guild, a cached yet unstored (as in a db) presence object is dispatched out displaying said information. And when a user unsubscribes the same process happens.

These both are asynchronous requests which don't block the server nor client to allow for snappy Gateway interactions. One thing though is that the Gateway stores member objects of the member which are online in a set. So let's say there are three people in a Guild and two are online, only two of those would be stored. Awesome, right?

The only worry I have is about scalability in terms of ram for servers over 1m members, but thankfully we don't have to worry about that for now.

While Member objects are stored in a Set, which changes based on any member updates, Presence objects are stored within a map tracking (user\_id, data) to simplify getting, modifying, and deleting data.

Now for a really main part: Sessions themselves.

Every part of a Derailed Gateway connection with a user is tied to a Session, a stateful GenServer which acts like a propeller for the handler. In v1, this will be expanded upon moving most if not all state from the handler to the Session itself to support resuming connections.

The \`Derailed.Ready\` module will stay but will be turned into an asynchronous GenServer and be a separate module to provide more scalability.

## Umbrella Design

For v1 we will be taking advantage of Elixir's Umbrella design and splitting up all our modules to a monorepo-based Umbrella project which can be separately scaled based on demand and load.