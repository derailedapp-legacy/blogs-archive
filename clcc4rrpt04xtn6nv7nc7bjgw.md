# Derailed: 2023

> Keep going, until you can't go further.

Derailed, while becoming one-year old last month, has always been flawed and faced with planning issues. Over most of our development and previous development cycle, Derailed has just gone with the flow and committed everything willy-nilly.

Starting for 2023, we want to change that, and our April 2023 API launch date shows that. Firstly we'll do features slowly and make sure they are tested and work, secondly, we're going to have multiple plans to extend our reach across multiple developers, and thirdly, build on open source at each stage and make sure our project can be open source to our most wanted extent.

One of the things which won't change is the easiness of such things as starting up or deploying, our focus is not on easiness but on speed, feature set, advances and customizability.

# Sticking to Roots

Back on December the 7th of 2021, roots and planning for Derailed started, the reason I made it was because I was utterly disappointed with the state of other Discord Alternatives. Revolt, while being most likely the best Discord alternative still lacks many features and its over-modularity construes it. Revolt also still does some rooky things, like giving the entire member list instead of a secluded part and not having any job/task scheduling.

Another Discord Alternative--Matrix--overcomplicates itself with its Federation System and turns developers away with its unfriendly API. While being a good alternative for security-focused beings, we all sadly know most internet users aren't security-focused.

## Roadmap till April

As the dawn of the new year, we've decided to strip our old roadmap, quite early though. To give a quick little flashback, I started working on this blog on the second of December. But that doesn't matter as all that matters is our plan.

GO means Gateway-only and RO Rest API-only.

### Rate Limiting

Because Discoursy's API will be made using Sanic, we decided to use the only current and up-to-date rate limiter for Sanic: [sanic-limiter](https://github.com/bohea/sanic-limiter).

Now that's decided, we should discuss specific rate limits.

The listed limits here are also subjects and are enforced to and by their parent limit. The root limit is the global limit, the global rate limit for every route throughout our entire API is 50 requests per second.

Now simply the rate limits are:

*   50/second Global
    
*   `/guilds/*` 10/second Guild
    
*   `/channels/*` 7/second Channel
    
    *   `/channels/*/messages/*` 5/5 seconds Message
        

Anything not here only follows the global rate limit.

### Job Queues

For our new API, job queues will start to be heavily implemented and used, especially for heavy processes like deleting guild members, and channel messages, or waiting to delete a user.

Every Job's max runtime (or approximately how long it can run until being canceled and possibly retried) is 18000 seconds or approximately 5 hours.

Personally, we'll be using the arq library, since in my opinion, it's the best asyncio-based job manager.

## Users

The people that'll be interacting the most with our API will be users, so we've made sure to make our objects and things surrounding users respectable and easily understandable.

*   id
    
    *   The user's id. Since Derailed uses Twitter's Snowflake ID, it'd be a stringed version of that.
        
*   username
    
    *   This user's username is the thing they use the most to identify themself throughout the platform. Maxed out at 30 characters.
        
*   discriminator
    
    *   A four-character stringed integer discriminator to prevent name squatting.
        
*   email
    
    *   The users' email, is used for identifying and logging in.
        
*   flags
    
    *   Flags were placed upon this user.
        
*   system
    
    *   If this user is a part of our system.
        
*   deletor\_job\_id
    
    *   The ID of the job which deletes this user. This is timed and set to 90 days after required (as stated in TOS)
        
*   suspended
    
    *   A boolean declaring if this account has been suspended or not.
        
*   pronouns
    
    *   A string maxed out at 10 represents your pronouns. i.e.: he/him, she/her, they/them, etc.
        

While being simple and minimal, that's kinda the point, cutting out unnecessary bits and stripping other parts.

## Guilds

Derailed Guilds serve as communities and let multiple people communicate throughout a vast landscape full of categories, channels, and other members. The owner gains **full** permission access and is treated as having the highest role even without having the highest role.

*   id
    
    *   a stringified Twitter snowflake.
        
*   name
    
    *   The name of this Guild
        
*   description
    
    *   A description of this Guild is given in Invites.
        
*   flags
    
    *   This Guilds' flags
        
*   color
    
    *   Integer representation of this Guilds' Color Scheme.
        
*   member\_count
    
    *   Estimated number of members in this Guild. Maxed at 1000
        
*   presence\_count
    
    *   Several present presences in this Guild. Maxed out at 1000
        
*   owner\_id
    
    *   id of the owner of this Guild
        
*   joinable Identifies
    
    *   if this Guild is joinable via an invite. Mostly for preventing raids
        
*   unjoinable\_reason
    
    *   The reason why we're not joinable
        

And when in the gateway, on a `GUILD_CREATE` or `GUILD_CACHE` event, we'll include a channels list which is pretty obvious what it is.

### Invites

Invites represent a fundamental part of Guilds. They allow new users to join Guilds and let people have vanity guilds.

*   code
    
    *   The invite's code
        
*   guild\_id
    
    *   The id of the managed guild id
        
*   author\_id
    
    *   The id of the author's id.
        

## Presences

Presences serve as a showing of your current activities.

*   id
    
    *   The user id of this presence.
        
*   guild\_id
    
    *   The guild this presence belongs to.
        
*   device
    
    *   The device this user is on. Can only be "mobile," and "desktop."
        
*   activities
    
    *   The activities this user in this Guild has.
        

### Activities

Every activity type has a name, type, and created\_at field.

Statusable activities include a content and status field, both being strings content being caped at 10 and status only being "online," "offline," and "dnd."

#### Game

Has a `game_id` for the Games Snowflake ID, and is a statusable activity.

#### Stream

Non-statusable activity including a stream object with stream platform details.

In the stream object:

*   platform
    
    *   "Twitch," or "YouTube" (not capitalized.
        
*   platform\_user
    
    *   The platform username (on YouTube it'd be your custom url or id)
        
*   stream\_id
    
    *   id of the stream on the platform. Doesn't show on twitch.
        

#### Custom

A statusable activity with an added emoji field for emojis.

## Channels

Channels are Discoursy's way of letting people interact with each other and have proper discourse. By all means necessary, **please** by no chance implement channels as guild-only. Certain types may be guild-only but by themselves, every channel type cannot.

Channels going through deletion should have a delete\_messages\_job\_id field and a shun field set to true.

Every guild Channel comes with an id field, a last\_message\_id field, and a name field.

### Messages

A message is a piece of content and data that can be sent within most Channels. They allow communication and also allow bots to receive messages to receive and reply to commands.

*   id
    
    *   This message's snowflake id
        
*   author
    
    *   The user/member who created this message
        
*   content
    
    *   The content of this message
        
*   channel\_id
    
    *   The id of the channel this message was sent in
        
*   timestamp
    
    *   The timestamp for when this message was sent
        
*   edited\_timestamp
    
    *   The timestamp for when this message was last edited
        

### ReadStates

A read state is a permanently stored state (unless your account is deleted) used to identify read channels.

*   id The Channel ID of this State
    
*   mentions The number of mentions this user has. Capped out and can't go above 51 (clients should do 50+ in that case.)
    
*   last\_message\_read The last message read by this user
    

### Guild Channels

Channels only visible or usable in guilds. Every channel here may be parented by a category channel. Every channel here may have a

#### Text Channel

Text Channels are messageable-only channels within a guild context.

They add no properties than the ones already given by guild channels and messageable channels.

#### Category Channel

Guild Channel which categorically sorts other channel types and parents' other channels.

It only has the guilds given by guild channels by default.

### Non-Guild Channels

Channels formed in or used within non-guild contexts.

All channels here are messageable and henceforth have ReadStates and a last\_message\_id field.

#### User-based

Channels formed by or used by users averagely.

##### User-to-User

A channel simply connects a user to another user.

##### Group Channel

A channel of multiple users. The creation of a group channel requires the creator to have a relationship with the members.

#### System Channels

System Channels are only usable within the system or used by the system for the user

##### Support Channels

Channels where users can ask for support from a representative and share details of it.

Comes with a representatives field that lists down user objects.

# Gateway

The Derailed Gateway is reliable for sending events to users and receiving processes from them.

It's the powerhouse of many processes, including our real-time dependency. By default d and op are always present.

Gateway messages are always JSON, encrypted and compressed using zstd as a standard.

## Independent Events

Independent Events are events that act like their own outside of normal events.

### Connect

OP Code: 0

Sent once connected to the WebSocket.

An empty WebSocket message with its default fields.

### Ready

OP Code: 1

Sent once authentication and necessary jobs have been completed.

*   version
    
    *   The API version being used
        
*   user
    
    *   Your current user object
        
*   guild\_ids
    
    *   ids of joined guilds
        
*   session\_id
    
    *   The id for this session
        

### Member Chunk

This is a response to a Guild Member Chunk request.

## Dependent Events

All dependent events have a t field, which is a fully capitalized version of the name here.

*   guild\_create
    
*   guild\_update
    
*   guild\_delete
    
*   guild\_prune
    
*   member\_join
    
*   member\_leave Has a `kicked` and `banned` boolean.
    
*   member\_update
    
*   channel\_create
    
*   channel\_update
    
*   channel\_delete
    
*   message\_create
    
*   message\_update
    
*   message\_delete
    
*   presence\_update
    
*   user\_update
    
*   invite\_create
    
*   invite\_delete
    
*   invite\_bulk\_delete
    

## Exact Implementation Details

While we're trying to do our best with everything here, we're not gonna put every detail possible. Things like routes, or the data routes accept will be laid down later on in our spec to make everything more organized and easier to understand.

Viva Derailed!