---
title: "Developer-friendly Derailed"
datePublished: Fri Oct 13 2023 13:30:23 GMT+0000 (Coordinated Universal Time)
cuid: clnonc3hz000e09jp06c35qmb
slug: developer-friendly-derailed
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/PGdW_bHDbpI/upload/d3eb32d3afe29e97f6c493b2754573b8.jpeg
tags: gateway, websockets, apis, rust, derailed

---

Welcome! I'm Vincent, creator and lead of Derailed. This article focuses on Derailed today and our plans for Derailed until its official launch.

# Rolling Back Tracks

Derailed's tracks aren't happening. At least not in their original Twitter-like format. Derailed will be shipping without tracks support, and primarily focusing on one thing: a stable, minimal, and efficient instant messaging solution for communities.

Now that doesn't mean I'm scrapping everything on our work with tracks. While our Next.js app will be scrapped, bits and pieces I made for it like the revamped Login and Register pages will be merged into Derailed's currently worked-on app, along with our API-side relationship implementation.

# Massive Backend Changes

We are currently planning massive changes to our backend. Firstly, we are going to move Derailed's API from our current library actix, to a more modern library and one which is just better for the people using our API: axum. This will be in a far while though as we are waiting for the next version of Axum to come out to avoid immediate breaking changes, and to first prioritize the next big change.

## Gateway: No More Elixir

Since December, Derailed's real-time service has been written in Elixir and used the Erlang VM. We plan for that to stop.

While Elixir and Erlang are extremely powerful tools for our use-case they sadly just don't fit too well with our current stack, and many of their syntax just doesn't fit with Derailed. To unify our whole backend (especially the database part middleware,) we are going to be rewriting our Gateway to Rust. Our new Rust-end Gateway will consist of several interconnected services:

* WebSocket Service (WSS)
    
* Presence Service (PS)
    
* Supreme Member Storage (SMS)
    

### WebSocket Service

The WebSocket Service will do as named. It will serve messages and events to users via WebSockets. It is the central part of our Gateway and forces itself to handle fallback, errors, and is as asynchronous as possible to serve as many users per instance as possible.

### Presence Service

Presences are very complex to store and handle. You have to mostly accurately be able to count and store them even when a user disconnects. This service exists to be an ever-online and also handles WSS nodes going down in failing cases. It uses a gRPC service to communicate bidirectionally towards the user and presence service.

### Supreme Member Storage

SMS aims to alleviate the burden of fetching members on our database. It eternally (as long as people in a Guild are online) stores every single member of a Guild (it actually depends on the size of the Guild, if your Guild is big you get the privilege of us not collecting offline members.)

SMS gets connected to the same way as the Presence Service, but instead of verifying who's online, it's to verify whether a Guild is actually in use (the bidirectional connection is used by the server to stream Member updates, and by the Client to request for Members.)

### Bringing Back... Redis!?

Yes! Derailed will be bringing back Redis to the Gateway (the protocol, internally we already use Dragonfly.)

Now of course this is temporary, and the only reason we aren't using Kafka is because we don't have to. In the future, once Redis can't scale with us (or once it becomes too slow because it *will*) we plan to make a Kafka-like system inside of Rust to serve our **exact** needs. That is:

* Higher amount of topics (we don't need thousands or millions, but *tens* of millions to hundreds of millions of topics.)
    
* Higher Throughput (Mostly avoiding Java's huge overhead at scale. This is in the same light as what ScyllaDB did to Cassandra, but without compatibility.)
    
* Server-side validation before sending messages (Embeds Lua to allow the server to validate whether a message should be sent to a certain Client.)
    

And much more. We want Kafka but much more efficient both on the machine and on the network. This will all be open-source once it happens and will probably be done by engineers mostly not named "Vincent."

# That's it again! For now...

That's it for now. I know I've not really done much here for a while, but I guess I'm back! Our plans here of course aren't final, they never are until they're done, but do expect most of this to become reality. The new Kafka-like system is a really interesting proposal which I will most likely explore further in the future to keep enhancing not just Derailed, but the entire developer ecosystem.

However, for now, you binge readers (again, does that even exist?) and people of the current, have a great rest of your day. Vincent here, signing out for now.