---
name: screenshot-router
description: Route a phone screenshot to the right Claude Code chat. Use when the user sends an image with little or no text in the screenshot inbox chat, or says "route this", "where does this go", or "send this to the right chat". Reads the screenshot, picks one destination from the routes file, and hands it over with send_message so that chat decides what to do.
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

Read the routes file. It lists every destination chat with what it takes and what it does not take. Look for it in this order:

1. `.claude/screenshot-routes.md` in the project this session runs on. This is the user's own file.
2. `routes.example.md` next to this skill. This only holds examples. If you end up here, tell the user to copy it to `.claude/screenshot-routes.md` and fill in their own chats, and offer to do it with them: list their sessions with `list_sessions` and draft a route for each active one.

1. If the screenshot names a client from the `clients` table, send it to that client's chat.
2. Otherwise compare against each route's **takes** and **not** lines and pick the best fit.
3. If two routes fit equally, take the one higher in the file.
4. If you are unsure between two (you would bet less than 70% on your pick), ask the user once with `AskUserQuestion`: the two candidates plus "Handle it here". Do not ask when one route is clearly right.
5. If nothing fits, use `inbox`: handle it in this chat.

A screenshot goes to exactly one chat. If it truly needs two, send it to the main one and mention the other in the handoff so that chat can pass it on.

## 3. Hand it over

Get this chat's own session ID with `get_session` (no arguments) so the destination can send things back. Load `get_session` and `send_message` from the claude-code-remote server first if they are not loaded yet.

### Check that the chat is online

Before sending, call `get_session` with the route's session ID and look at three fields:

- `session_status` is `SESSION_STATUS_ARCHIVED`: the chat is gone. Do not send. Tell the user and offer to handle it here or start a new chat for that route.
- `environment_kind` is `bridge` and `connection_status` is not `connected`: the chat runs on the user's own computer and that computer is off or Claude is not running there. The message would wait until it comes back online, possibly for days. Do not send silently. Ask the user with `AskUserQuestion`: send anyway (it waits until the computer is on), send to another route, or handle it here.
- Anything else (a cloud chat, or a connected computer): send. A cloud chat starts up when the message arrives.

Mention the check in your one-line confirmation when it mattered, for example "Sent to LinkedIn, but that chat is offline; it picks this up when your computer is on."

### Send

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

If `send_message` fails, tell the user, name the route, and offer to handle it here or to start a new chat for that route with `create_session`. Do not pick a different route silently.

## 4. Tell the user

One line, nothing more:

> Sent to **LinkedIn content**: infographic with 245 reactions, good format to adapt.

## Automatic mode: the Drive inbox

Optional. The user's phone uploads every new screenshot to a Google Drive folder, and a scheduled Routine fires into this chat with a message like "Check de screenshot-inbox" or "Check the screenshot inbox". Setup steps for the user are in `docs/automatische-inbox.md` in the plugin repository.

The folder IDs are in the `## drive-inbox` section of the routes file (`inbox folder` and `done folder`). If that section is missing, tell the user automatic mode is not set up and point them to the setup guide. When the routine fires:

1. Use the Google Drive connector. `search_files` with `parentId = '<inbox folder>' and mimeType contains 'image/'`. These are the unprocessed screenshots (the done folder is excluded because its files have another parent).
2. If there are none, end the turn with one short line ("Geen nieuwe screenshots."). Do not message anyone.
3. For each file, oldest first: read it with `read_file_content`. That gives the text on the image plus labels; use it as the transcription in step 1 above. Add the Drive link (`viewUrl`) to the handoff as `Original image:` so the destination chat can open it.
4. **Skip private screenshots.** Every screenshot on the phone ends up here, not only work. Do not route anything that is clearly personal or sensitive: bank and payment screens, login or 2FA codes, passwords, medical information, private chats with family or friends, personal photos, tickets and receipts that have nothing to do with work. Move those to the done folder without sending, and count them as skipped. When in doubt whether something is work, skip it and list it in the summary so the user can forward it by hand.
5. Route the rest as in steps 2 and 3 above, including the online check. In automatic mode there is nobody to answer `AskUserQuestion`: if you are unsure between two routes, pick the higher one in the routes file and say so in the summary; if the destination is offline, send anyway (it waits) and say so in the summary.
6. After a file is sent or skipped, move it with `update_file` (`parentId = '<done folder>'`). Move it only after the send succeeded, so a failed send is retried on the next run.
7. If the Google Drive tools are not available in this chat, say so in one line and stop; do not guess.
8. End with a short summary: per screenshot one line with where it went, plus the number skipped as private. If more than 10 are waiting, handle the 10 oldest and leave the rest for the next run.

## Receipts

Each destination chat sends back a line starting with `[Screenshot handled]`. When one arrives, pass it on to the user in one line ("SEO chat filed the reel as a risk to watch"). Do not reply to the destination chat.

When the user asks "is this working?" or "what happened with my screenshots?", list each screenshot routed in this chat with its destination and its receipt, or "no receipt yet". Do not try to page through the destination chat's full history to find out; those histories are too long. If a receipt is missing after a day, send that chat a short `[Screenshot check]` message asking for it.

## Bounced screenshots

When a message starting with `[Bounced screenshot]` arrives, re-route it using the reason given, and never send it back to the chat that bounced it. If no other route fits, handle it here and tell the user.

## Keeping the routes current

If the user says "new route", "this chat is gone" or similar, edit `.claude/screenshot-routes.md`, commit, and push. When a send fails because a session is archived, suggest removing or replacing that route.
