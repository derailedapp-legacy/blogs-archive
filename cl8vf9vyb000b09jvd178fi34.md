## Derailed's State of the Platform for October

Well... it's been an eventful month, let's cover it!

## Summary
After so many months, to be exact February, Derailed has finally got its entire stack (of which is our REST API, Gateway, and Website) all hooked together with Nginx.

We've made the new [derailed-app](https://github.com/derailedapp/derailed-app) and [january](https://github.com/derailedapp/january) repositories to store our code for our Web App, Website, and Nginx configuration.

Our entire stack has been tightly hooked together using both docker and docker-compose, making it easier to deploy Derailed than ever before.

But with our summary complete, let us get to the juicy main bits.

## Derailed API

The Derailed API has witnessed quite a few new additions and changes. One of the sad things is it's out of date with the Ekranoplan (it has more features than what the Ekranoplan covers), which we'll have to fix in the future.

The biggest change most likely is that we've finally finished our implementation of tracks and guilds. This means we've implemented:

- Guilds themselves
- Messages
- Tracks
- Group DMs
- Relationships ^

Which is a massive feat.

## Derailed Gateway

The Derailed Gateway has not witnessed many changes (mostly because it features most of what we need) else than a few bug fixes, so it's not gonna be covered here.

## Derailed App

This is our app **MVP**, meaning in the future we're going to be rewriting this app to build the best experience possible. Think of this as a minimalistic demo used as proof of concept and testing.

## January

January is our Nginx config and code, it proxies all of our routes and is used as a middle man between Cloudflare and our product.

# There goes another month, completed.
And that's it for this month folks. Another wrap in the basket as you can say.

Sorry for being partially late on this, I've been a bit busy with school and other activities which explains why progress has mostly slowed, although even faced with this I'm trying to keep the project going because I believe in its principles and responsibilities.
As always, stay healthy, stay hydrated, and stay derailed.