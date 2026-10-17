# Screenshot routes

Each route is one Claude Code session that can receive a screenshot. The router reads this file every time, so edit it whenever you start, rename or archive a chat.

Fields:
- **session**: the session ID (`session_...`). Find it in the session URL, or ask the router chat to run `list_sessions`.
- **takes**: what kind of screenshot belongs here. The router matches on this, so be concrete.
- **not**: what looks similar but belongs somewhere else.

When two routes fit equally well, the one higher in the list wins.

The routes below are examples. Replace the session IDs with your own and delete what you don't use.

---

## clients
Client-specific chats. If the screenshot names one of these clients, it goes to that client's chat, whatever the topic.

| Client | Session |
|---|---|
| Example Client A | session_REPLACE_ME |
| Example Client B | session_REPLACE_ME |

## linkedin
- **session**: session_REPLACE_ME
- **takes**: LinkedIn posts, carousels, infographics or comments worth reacting to or using as inspiration; LinkedIn analytics; anything about the LinkedIn algorithm.
- **not**: LinkedIn DMs from prospects (go to `outreach`).

## social
- **session**: session_REPLACE_ME
- **takes**: Instagram, Facebook, YouTube and TikTok posts, reels, stats and inspiration.
- **not**: reels whose topic is SEO, sales or another route; the topic decides, not the app.

## website
- **session**: session_REPLACE_ME
- **takes**: website designs, landing pages, UI ideas, bugs on your own site, competitor sites.

## seo
- **session**: session_REPLACE_ME
- **takes**: SEO and GEO tactics, backlinks, AI search answers (ChatGPT, Perplexity, Gemini, AI Overviews), Search Console screenshots.

## outreach
- **session**: session_REPLACE_ME
- **takes**: leads, prospects, DMs, contact details, companies to approach.

## email
- **session**: session_REPLACE_ME
- **takes**: emails, newsletters, things to unsubscribe from or reply to.

## inbox
- **session**: (none, handled in the router chat itself)
- **takes**: anything that fits nowhere above, or a quick question you want answered right away.
