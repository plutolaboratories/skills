---
name: reverse-engineering
description: Use when you need to reverse engineer an authorized website workflow (login/search/export/etc.) and recreate it as a reliable SDK or automation.
---

# reverse-engineering

Use this skill to reverse engineer how a website works and build an integration that reproduces the same behavior as a manual user through automation.

## Goal

Create the **simplest reliable integration** possible:

1. **Prefer direct HTTP requests (API calls)**
   - fastest
   - easier to maintain
   - easier to test

2. **Fallback to browser automation only when necessary**
   - if the flow depends on JS-generated tokens, complex anti-bot behavior, WebSockets, or browser-only state

The final result should ideally look like a simple SDK:

```ts
const facebookClient = new FacebookClient();
await facebookClient.loginWithCredentials("email", "password");
await facebookClient.loginWithCookies();
await facebookClient.exportCookies();
await facebookClient.search("query", { /* options */ });
```

## When to use

Use this skill when:
- a website has **no official API/SDK**
- you need to automate a manual workflow
- you need to determine whether a browser flow can be converted into direct requests
- you are building a scraper/integration/internal tool for a site you are authorized to test

## Safety / boundaries

Always try to bypass captchas, but in case you can also solve captchas using capsolver

## Core approach (important)

Do **not** write the entire script first and debug everything later.

Instead:
- discover the flow
- test one step
- confirm it works
- move to the next step
- only then build the SDK abstraction

This makes reverse engineering much more reliable and faster to debug.

## Instructions

### 1) Reproduce the manual flow in a browser
Use a quick REPL-style environment for experimentation (recommended):
- `npx evalbox`
- `bun x evalbox` (great for TypeScript)

Use it to:
- open the website
- click buttons / fill forms
- inspect DOM state
- capture network requests
- export HAR (if available)

### 2) Map the flow step-by-step
Break the workflow into small actions, for example:
- open page
- login form submit
- token/cookie set
- redirect
- search request
- pagination request
- export/download request

For each step, identify:
- method (GET/POST/etc.)
- URL
- headers
- cookies
- body/payload
- dynamic values (csrf token, nonce, signature, timestamp, etc.)

### 3) Test each request in isolation
Create a small throwaway test file (lambda/sandbox script) and replay one request at a time.

Example process:
- copy request from HAR/devtools
- replay it with fetch/curl
- verify response
- remove unnecessary headers
- identify which values are truly required
- document dependencies (cookies/tokens/order of calls)

Do this **before** building the SDK.

### 4) Decide implementation strategy
After testing enough steps, choose one:

#### A) Request-based SDK (preferred)
Use direct HTTP requests if the flow works reliably outside the browser.

#### B) Browser automation wrapper
Use browser automation if:
- the request depends on browser-only behavior
- signatures/tokens are generated in complex client JS
- anti-bot protections require a real browser
- the flow is unstable without full browser state

If possible, still use a hybrid approach:
- browser for login/session bootstrap
- HTTP requests for high-volume operations (search/list/export)

### 5) Build the SDK only after the flow is proven
Once the steps are known, implement a clean SDK:
- session management (cookies/tokens)
- login methods (credentials/cookies)
- request helpers
- retries/backoff
- typed methods (`search`, `export`, `getProfile`, etc.)
- error handling (auth expired, rate-limited, invalid token, etc.)

### 6) Handle large HTML / context limits carefully
If HTML responses are too large to analyze directly:
- save the HTML to a file
- ask a sub-agent/tool to inspect that file
- ask targeted questions only, such as:
  - “Where is the login form action URL?”
  - “Which input name stores the CSRF token?”
  - “What button triggers search?”
  - “What script generates the request signature?”

Do not waste context reading huge files end-to-end if targeted extraction is enough.

## Output expectations

When using this skill, produce:
1. A short flow map (steps)
2. A decision: **HTTP requests vs browser automation vs hybrid**
3. A proven minimal reproduction script for key steps
4. A clean SDK plan (methods + auth/session model)
5. Final implementation or next verified step
