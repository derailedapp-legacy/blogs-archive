## Derailed Architecture

From afar, and looking at wrappers and such, Derailed may seem like a simple API.
But internally, it is far different.

From the start, the way derailed was structured and how it was built led to several advanced techniques being used. Thankfully, since we use python it wasn't as hard to implement these as they would be in other languages.

If you want to get a glimpse at what we would call advanced, here's a sneak:

## Handling Permissions cheaply

We currently only have three database calls that are required to get users permissions.

It starts with:
  - Make sure the user is a member (only on things like tracks) then
  - Getting the users roles in the said guild and finally
  - Getting the guild itself

These three all play a massive purpose in our permission system. Every time we need users permission, we do these two or three database calls.

## Moving Positions of Roles & Tracks

This alone accounts for massive amounts of database calls.

The max tracks in a guild can be 500 and the max roles can be 200,
meaning we could be pulling up to 500 database calls on a change. That's massive, and since MongoDB doesn't have ACID transactions these are all separate.

The way we deal with it is something like this but much more advanced and with error handling:

```py
async def get_position(guild_id: str, role: Role, position: int) -> None:
    # this is the @everyone role, and 0 is not a valid position.
    if position in {0, 1}:
        raise HTTPException(400, 'Cannot designate role position')

    highest = await get_highest_role(guild_id=guild_id)

    if position == highest.position + 1:
        await role.update(position=highest.position + 1)
        return

    roles = Role.find(Role.guild_id == guild_id)

    async for grole in roles:
        if grole.position > position:
            await grole.update(position=grole.position + 1)
        elif grole.position == position:
            await grole.update(position=grole.position + 1)
            break
        else:
            await grole.update(position=grole.position - 1)

    await role.update(position=position)
```

Simply, this either adds or subtracts the role's current position based on the other role's new position and its current position.

This still does pull many database calls, but at the current moment, we have no other real choice.
Although, we're currently exploring using `update_many` which could more cheaply update positions in bulk.

## Gateway DDoS Prevention

While DDoS attacks are always a possibility, we'd love to have the ability to limit them as much as possible. We use Cloudflare, plus our layer of advanced rate limiting which we're exploring rebuilding, to accommodate this goal for our REST API. But, while this can help the REST API, our Gateway API was still lacking in rate-limiting procedures. WebSocket rate limiting is still quite a new topic surprisingly, and especially for python finding examples or other such is impossible. So, using the [limits](https://limits.readthedocs.io) library, we were forced into implementing our raw version of it.

It works quite simply, when a user makes any interaction, such as sending a message, their bucket gets hit. If the bucket is full, they are immediately thrown off the connection. There are also a lot more complexities, like per-day connection rate limits but in a basic format, it's just this.

## Relational-like Actions in a Document Database

If you know, Derailed uses MongoDB, which is a document DB. We use it because it allows us to develop our new versions of software and other such at lightning speed.

A big problem with it though is it is not relational, so we forged a different system that would also happily decrease network i/o for failed or faulty requests.

Instead of forcing the database to get the object every time we call it, we could just pull a `{object}_id`. This would both allow us to use only the object ids in future calls and also decrease network i/o by not requiring us to receive the object when only wanting its id or other such.

## Combining the Values of Permissions
Combining the permission bitint values of roles are essential to a functional role system.

We had to make a function that was both types friendly and was able to combine values of roles. This wasn't the easiest task and took a bunch of testing to get right.

In a simplified sense, it does something like this:

```py
def combine_role_permission_values(*permission_values: PermissionValue):
    modified_value = 0
    _internal = []

    for value in permission_values:
        _internal.insert(value['position'], value['value'])

    for value, permission_value in itertools.product(_internal, RolePermissionEnum):
        if has_bit(modified_value, permission_value.value) and not has_bit(
            value, permission_value.value
        ):
            modified_value -= permission_value.value
        elif not has_bit(modified_value, permission_value.value) and has_bit(
            value, permission_value.value
        ):
            modified_value |= permission_value.value

    return modified_value
```

This both adds and removes permission values while staying type friendly by using enums and typed dictionaries.

## The end.. for now
And that's just a few of the advanced problems we've faced while building the infrastructure required to build such a platform. I plan to make a follow-up article in the future once we're finished to give somewhat of a progress report.

If you like such problems like these and want to help out, you can [volunteer here](mailto:volunteer@derailed.one).