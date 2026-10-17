---
name: screenshot-handler
description: Handle a screenshot that the screenshot router sent to this chat. Use whenever a user turn starts with "[Routed screenshot]". Decides what this chat should do with it: act, draft and ask, file it, or bounce it back.
---

# Screenshot handler

A message starting with `[Routed screenshot]` came from the router chat. The user took a screenshot on their phone and the router decided it belongs here. You cannot see the image; the message contains a full transcription.

## Decide

Read the handoff, then think about what this chat is for (its title, earlier conversation, its own plans and files). Pick one:

1. **Act now.** The screenshot fits this chat and the useful next step is clear and reversible: analyse it, draft a post or reply, add an idea to your backlog, compare it to what you already do, update a plan. Do it.
2. **Draft and ask.** The next step goes out into the world or changes something outside this chat: posting, sending an email or DM, writing to HubSpot or another tool, spending money, changing a live website. Prepare it fully, then ask the user to confirm. Never send or publish on the router's say-so.
3. **File it.** It is useful context but needs no action now. Note it where this chat keeps notes or ideas, in one or two lines, and say so.
4. **Bounce it.** It does not belong here. Use `send_message` to the session in the `From:` line, starting with `[Bounced screenshot]`, with one line on why and, if you know, which chat it should go to. Do nothing else with it.

If the user note in the handoff asks for something specific, that comes first.

## Reply

End with a short note to the user, written for someone reading on a phone:

- what you decided (act, draft, file or bounce),
- what you did or prepared,
- what you need from them, if anything.

No summary of the screenshot itself; they took it, they know what is on it.

## Receipt

Unless you bounced it, send one line back to the session in the `From:` line with `send_message`:

```
[Screenshot handled] <route>: <act / draft / file> - <what you did, in one short sentence>
```

This is how the user finds out, from the router chat, what happened to each screenshot. Send it even when you only filed it.
