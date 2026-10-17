---
name: screenshot-router
description: Route a phone screenshot to the right Claude Code chat. Use when the user sends an image with little or no text in the screenshot inbox chat, or says "route this", "where does this go", or "send this to the right chat". Reads the screenshot, picks one destination from routes.md, and hands it over with send_message so that chat decides what to do.
---

# Screenshot router

The user takes a screenshot on their phone and drops it in this chat. Your job is only to sort it. You do not do the work yourself; the destination chat does.

## 1. Read the screenshot

Look at the image and write down, for yourself:

- **Source**: which app or site (LinkedIn, Gmail, WhatsApp, a website, Search Console...).
- **What it is**: one plain sentence.
- **All visible text**, verbatim, in its original language. The destination chat cannot see the image, so this transcription is all it gets. Include headings, table rows, names, numbers, dates, URLs. Skip phone chrome (clock, battery, nav bar).
- **Names**: people, companies, clients, tools.
- **The user's note**, if they typed anything with the image. A note beats your guess.

## 2. Pick one route

Read `routes.md` in this folder. It lists every destination chat with what it takes and what it does not take.

1. If the screenshot names a client from the `clients` table, send it to that client's chat.
2. Otherwise compare against each route's **takes** and **not** lines and pick the best fit.
3. If two routes fit equally, take the one higher in the file.
4. If you are unsure between two (you would bet less than 70% on your pick), ask the user once with `AskUserQuestion`: the two candidates plus "Handle it here". Do not ask when one route is clearly right.
5. If nothing fits, use `inbox`: handle it in this chat.

A screenshot goes to exactly one chat. If it truly needs two, send it to the main one and mention the other in the handoff so that chat can pass it on.

## 3. Hand it over

Get this chat's own session ID with `get_session` (no arguments) so the destination can send things back.

Load `send_message` from the claude-code-remote server and send the handoff below to the route's session. Keep the header line exactly as written; the receiving chat uses it to recognize a routed screenshot.

```
[Routed screenshot]
Route: <route name>
Why here: <one sentence>
From: <this session ID>
Received: <date and time>
User note: <the user's text, or "none">

What it shows: <one sentence>
Source: <app or site>

Full text:
<verbatim transcription>

Names: <people, companies, tools>

How to handle this: you are the right chat for this, so decide what to do with it
in your own area. Do clear, reversible work right away (analysis, drafts, notes,
ideas, updating your own plans). For anything that goes out or changes something
outside this chat (posting, emailing, writing to a CRM, spending money), prepare it
and ask the user first. If it does not belong here after all, send it back to
<this session ID> with send_message, starting with "[Bounced screenshot]" and a
one-line reason. End with a short note to the user on what you did.

When you are done, send one line back to <this session ID> with send_message:
"[Screenshot handled] <route>: <act / draft / file> - <what you did>".
The router uses this to tell the user what happened.
```

The handling paragraph is in the message on purpose: it works even if the destination chat does not have the `screenshot-handler` skill.

If the session is archived or `send_message` fails, tell the user, name the route, and offer to handle it here or to start a new chat for that route with `create_session`. Do not pick a different route silently.

## 4. Tell the user

One line, nothing more:

> Sent to **LinkedIn content**: infographic with 245 reactions, good format to adapt.

## Receipts

Each destination chat sends back a line starting with `[Screenshot handled]`. When one arrives, pass it on to the user in one line ("SEO chat filed the reel as a risk to watch"). Do not reply to the destination chat.

When the user asks "is this working?" or "what happened with my screenshots?", list each screenshot routed in this chat with its destination and its receipt, or "no receipt yet". Do not try to page through the destination chat's full history to find out; those histories are too long. If a receipt is missing after a day, send that chat a short `[Screenshot check]` message asking for it.

## Bounced screenshots

When a message starting with `[Bounced screenshot]` arrives, re-route it using the reason given, and never send it back to the chat that bounced it. If no other route fits, handle it here and tell the user.

## Keeping routes.md current

If the user says "new route", "this chat is gone" or similar, edit `routes.md`, commit, and push. When a send fails because a session is archived, suggest removing or replacing that route.
