# Screenshot router for Claude Code

[Nederlands](README.nl.md)

Take a screenshot on your phone, share it into one Claude chat, and it ends up in the chat that should deal with it. That chat then decides what to do.

I run a marketing agency with a separate Claude Code chat for each job: LinkedIn, SEO, the website, outreach, each client. I kept taking screenshots of posts, reels and emails and then never opening them again, because I first had to work out which chat they belonged in. This fixes that.

## How it works

```
phone screenshot
      │  share into the inbox chat
      ▼
screenshot-router ── reads the image, copies out all text,
      │              picks one route from your routes file
      │  send_message
      ▼
destination chat ── screenshot-handler decides:
      │              act now / draft and ask / file it / bounce back
      │
      └── one-line receipt back to the inbox chat
```

1. **Read.** The router reads the screenshot and writes out everything on it: the source app, all visible text word for word, names, numbers and links. The destination chat only gets text, so this has to be complete.
2. **Route.** It compares the screenshot with your routes file, a short list of your chats and what each one takes. Client names win first. Then the topic counts, not the app: an Instagram reel about SEO goes to the SEO chat. If it can't decide between two, it asks you once.
3. **Hand over.** It sends a fixed message to that chat with `send_message`. The handling rules are written into that message, so it also works for chats that don't have the handler skill.
4. **Decide.** The receiving chat picks one of four outcomes:
   - **Act now:** work it can undo, such as analysis, drafts, notes or plan updates.
   - **Draft and ask:** anything that goes out, such as a post, an email, a CRM write or spending money, gets prepared and waits for your OK.
   - **File it:** useful context with no action needed.
   - **Bounce it:** wrong chat; it goes back to the router with a reason.
5. **Receipt.** The chat sends one line back ("LinkedIn: draft - wrote a reply post, waiting for your OK"). Ask the inbox chat "status?" to see what happened to each screenshot.

## Setup

You need Claude Code on the web (claude.ai/code) with the claude-code-remote tools (`send_message`, `get_session`), which cloud sessions have.

**1. Install the plugin**

```
/plugin marketplace add GoldLemonAI/Screenshot-router
/plugin install screenshot-router@goldlemon
```

Or copy the two folders in `skills/` into `.claude/skills/` in your own repository, and merge that to the default branch so new sessions load them.

**2. Add your routes**

Copy `skills/screenshot-router/routes.example.md` to `.claude/screenshot-routes.md` in the repository your sessions run on, and replace the examples with your own chats and their session IDs. The **takes** and **not** lines matter most, so write them concretely. You can also just send a screenshot: if no routes file exists, the router offers to draft one from your open sessions.

**3. Make an inbox**

Start one session as your inbox, for example "Screenshot inbox", and pin it in the Claude app on your phone. Share a screenshot from your phone into that chat. No text needed; add a note if you want something specific.

## Limits

- Only Claude Code sessions can receive screenshots. Regular claude.ai chats and Projects can't be messaged.
- The destination chat gets a full transcription, not the image. That works well for posts, emails and dashboards and less well for purely visual design feedback.
- From a video you only get what's visible in the frame.
- Receipts reach the inbox chat as messages; you see them the next time you open it.
- Nothing happens automatically when you take a screenshot. You share it into the inbox chat yourself.

## Files

| File | What it does |
|---|---|
| `.claude-plugin/` | Plugin and marketplace manifest |
| `skills/screenshot-router/SKILL.md` | Reading, routing, the handoff message, receipts and bounces |
| `skills/screenshot-router/routes.example.md` | Example routes; your own go in `.claude/screenshot-routes.md` |
| `skills/screenshot-handler/SKILL.md` | How a receiving chat decides and reports back |

## License

MIT. Built by [GoldLemon](https://www.goldlemon.nl).
