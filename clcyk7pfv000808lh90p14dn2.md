# Derailed Gateway: A Short Scale Overview

This is a pretty long ramble and overview about the technical design and redos that will change in Gateway v1.

In config/runtime.exs, the hash ring is setup getting the respective GUILD\_NODES, READY\_NODES, and SESSION\_NODES environment variables. These are then transfered to Discords ex\_hash\_ring, and stored globally via Discords FastGlobal.

Then when calling any function, initiating any GenServers needed and looking up GenServers with GenRegistry, you use the Node via a hash ring search up.

For Guilds, it is their respective id, for users it's their user id to support gRPC's required publishing, and for ready it is the Session ID.

On gRPC Services, the Server just looks up the node from the hash ring and does what it needs.

Thankfully, lookups to the ring even if giant are fast, taking up to 1.7 microseconds at max and 0.2 microseconds in normal operation.

Another thing is the time it takes to send the message itself. Thankfully Erlang has us covered with this one, where it only takes around 1 microsecond to send a message from one node/process to another.

This will both help with scalability, where everything can be scaled by themselves, and with speed since the base Erlang distribution carton isn't the best for our use case.

One final thing is related to Guilds and Sessions: starting with v1, to improve stability and decrease the likelihood of crashes, Guilds will start monitoring over sessions and remove any which go down, and same with Sessions but to Guilds.

For this task, we will be using ZenMonitor as it's faster than the base process monitor. If a Guild does go down, the session can try to reconnect with an exponential backoff to not overload the node.

If a session goes down though, a monitoring WebSocket process would receive such message and close the WebSocket (could be better trying to reconnect with a backoff in the future) with a code.

Monitoring processes like this improves stability by: not overloading nodes, letting Processes restart, and letting Sessions reconnect. It also reduces the possibility of dead sessions, by removing them before messages can be sent to them, which would reduce load and bandwidth.

In the end, this infrastructure is not critical at the current moment. The reason I am over engineering like this is because: 1 it doesn't matter too much because it's still quite easy to deploy, and 2 so in the future we don't have to worry about scaling too much.

The only "scaling" problem we may face in the future is high write tension with PostgreSQL, but by that time it wouldn't be too costly to move to the write-friendly ScyllaDB.

Stuff which could suffer from low write speed is the obvious: messages, reactions, basic things like that.

Things like members though will always stay on Postgres because of Scylla's primary key limitations (basically, Scylla is only fast when duplicated primary keys max out at 100mb. And this is bad because: 1. Members cannot use only a User or only a Guild as the PK and 2. Scylla doesn't allow querying with the PKs so we couldn't do both, which forces us to keep them on Postgres.)

And that's it, basically everything about scaling I've been thinking of for Derailed. Obviously, I will be doing benchmarks soon to emphasize any speed outputted by the Erlang VM, and our API.

This is a new advancing period for Derailed, we're slowly getting ready for a launch adding everything that we want for the minimal launch. If you want to help out in any way but don't know how to code, you could always support us on our Patreon! It's just https://patreon.com/derailedapp.

Good morning, Good evening, or Good night wherever you are, wish you the best of your week. Goodbye.