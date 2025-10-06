+++
title = "Fast Api Replaced By Zola"
description = "So I wrote the blog in fast api only to deploy zola at the end..."
date = 2025-10-06
[taxonomies]
tags = ["zola", "python", "fastapi"]
[extra]
# toc = true
# toc_inline = true
+++

## Created a Fast Api Blog

At first, I thought about building the blog as another Spring microservice. Too heavy, I thought immediately. I already have a basic
website running on Spring Boot and waiting for the containers to fire up just to change a link to this blog, well — why keep the overkill mode on, right?

{% crt() %}
```
2025-10-06T15:29:09.000000000Z [inf]  Starting Container
2025-10-06T15:29:10.740704036Z [inf]  
2025-10-06T15:29:10.740714150Z [inf]    .   ____          _            __ _ _
2025-10-06T15:29:10.740722455Z [inf]   /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
2025-10-06T15:29:10.740728764Z [inf]  ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
2025-10-06T15:29:10.740734024Z [inf]   \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
2025-10-06T15:29:10.740740239Z [inf]    '  |____| .__|_| |_|_| |_\__, | / / / /
2025-10-06T15:29:10.740745637Z [inf]   =========|_|==============|___/=/_/_/_/
2025-10-06T15:29:10.740751725Z [inf]  
2025-10-06T15:29:10.740767721Z [inf]   :: Spring Boot ::                (v3.5.3)

```
{% end %}


So, what’s the next option? Well, I’m learning Python, and FastAPI
is just… sweet! I went with Python for my blog, and of course, it was a very pleasant experience. I really like FastAPI,
and I’m definitely not the only one. I finished the blog API service in no time. You can check out the repo here: [https://github.com/charamzic/blogpy-api](https://github.com/charamzic/blogpy-api).

You can also check the [swagger](https://blogpy-api-production.up.railway.app/docs/) - it comes automatically with FastAPI. Don’t try to call the endpoints without an API key, though.

Done. Simple. Fast. Functional. But, as usual, I left it hanging for a while, during which I explored new stuff on the INTERNET!

## Created a Zola Blog

One night, just before falling asleep, I came across this interview. The interview itself wasn’t amazing, but a few things stuck with me.

- [Nik Revenco](https://github.com/nik-rev) is a smart guy.
- Maybe it's time to give [Helix](https://helix-editor.com) another try.
- I'll write my blog with [Zola](https://www.getzola.org).

{{ youtube(id="AS7mnDgFgnw") }}

I used to have a blog with [Hugo](https://gohugo.io) and working with it was seamless. So why not try something similar?
And that’s what I did. You’re now looking at a Zola blog with the Duckquill theme (links in the footer, btw). I’m writing it in Helix.

These are my first lines, besides copying my [old article](/posts/turning-old-macbook-into-an-ubuntu-server-part-1) from Substack. Now I'm fully commited to writing on
a regular basis... though, let's see, considering I possess a strongly developed weak will.

That's it. It is still likely that I'll use the fast api service at the end, or write something completely different, but for now, let us enjoy Zola.
