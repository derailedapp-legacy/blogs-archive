# Modernizing Derailed: Discoursy

Welcome! This is the official blog announcing the migration and modernization of multiple Derailed things, like branding or code.

## Branding

Derailed for a long while has switched and consorted over branding. Hell, there's an entire list of 40 or so names we've used over the entire lifespan of Derailed. But with our 1 year anniversary approaching, we felt the need to finalize the biggest issue throughout our branding crisis: the name.

When I picked derailed many months ago, I never expected it to be our final name more like a temporary name awaiting for the *perfect* name. One of the problems with the name that I had was it wasn't pluralizable, even if you try, it's a real tongue twister to say.

So from the flamming ashes of many months of debates, unwarranted changes, and too much fights to count, I'd like to formally announce Derailed's new and final name: Discoursy.

Picked apart from the word discourse, it's meant to emphasize the platform is suppose to be a place of discourse.

## Code Quality

As announced previously, Derailed is currently closed source, this is mostly done to improve code quality behind the scenes and release the code publicly in it's most prestigious form.

While not being the initial spark which set me to creating Derailed, it is one of the things that motivated me to keep going forward and forced down every rewrite.

One of things this rewrite will be doing is replacing our previous framework, FastAPI, with the Sanic framework to improve speed, developer experience and prevent code duplication, especially in places such as our rate limiter.

We're also going to be massively simplifying our API removing any past terms for files, or unimplemented features. We'll also be making our API and Gateway interconnected to increase the easiness of startup.