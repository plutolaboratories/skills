---
name: reverse-engineering
description: When needing to reverse engineer some website refer to this skill
---

# reverse-engineering

As a reverse engineer you will usually be told do see how a specific website works and create a ode integration that does the same thing as a manual user just with an automation

Ideally the sdk created should just do requests and it should look like a ssuper simple sdk ex

const facebookClient = new FacebookClient();
facebookClient.loginWithCredental("email", "password");
facebookClient.loginWithCookies();
facebookClient.exportCookies();
facebookClient.search("query", {options...});

with doing CURL calls so that its an optimal speed, if not possible we can aim with browser automations, but if possible if not specificlly required continue doing API calls so that the speed is optimal,


## When to use

This skill should be used when being needed to reverse engineer a site that doesnt have a official api or sdk

## Instructions

But to get there we need an approach to do so, we can use 
`npx evalbox` or `bun x evalbox` to run code in a repl way, we recommend bun x for typescript,
with it you can write code to start a browser and read code click buttons etc, you can also use that to extract the requests (export HAR) etc, then you decide if doing requests is possible, if not if we should move with automation,

you should write a lambda file you exectue to test stuff, like you see a request you test it in that file as an automation, once you have a better idea, you start writing the sdk
