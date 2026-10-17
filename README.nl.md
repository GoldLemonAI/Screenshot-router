# Screenshot-router voor Claude Code

[English](README.md)

Maak een screenshot op je telefoon, deel hem in één Claude-chat, en hij komt terecht in de chat die er iets mee moet. Die chat beslist daarna zelf wat er gebeurt.

Ik heb een marketingbureau en gebruik voor elke taak een aparte Claude Code-chat: LinkedIn, SEO, de website, outreach, en één per klant. Ik maakte steeds screenshots van posts, reels en e-mails en keek er daarna nooit meer naar, omdat ik eerst moest bedenken in welke chat ze thuishoorden. Dit lost dat op.

## Hoe het werkt

```
screenshot op je telefoon
      │  delen in de inbox-chat
      ▼
screenshot-router ── leest de afbeelding, neemt alle tekst over,
      │              kiest één route uit je routebestand
      │  send_message
      ▼
ontvangende chat ── screenshot-handler beslist:
      │              meteen doen / voorbereiden en vragen / bewaren / terugsturen
      │
      └── bevestiging van één regel terug naar de inbox-chat
```

1. **Lezen.** De router leest de screenshot en schrijft alles uit wat erop staat: de app, alle zichtbare tekst woord voor woord, namen, cijfers en links. De ontvangende chat krijgt alleen tekst, dus dit moet volledig zijn.
2. **Routeren.** Hij vergelijkt de screenshot met je routebestand, een korte lijst van je chats en wat elke chat ontvangt. Klantnamen gaan voor. Daarna telt het onderwerp, niet de app: een Instagram-reel over SEO gaat naar de SEO-chat. Twijfelt hij tussen twee chats, dan vraagt hij het je één keer.
3. **Overdragen.** Hij stuurt een vast bericht naar die chat met `send_message`. De regels voor de afhandeling staan in dat bericht zelf, dus het werkt ook bij chats die de handler-skill niet hebben.
4. **Beslissen.** De ontvangende chat kiest een van vier uitkomsten:
   - **Meteen doen:** werk dat terug te draaien is, zoals een analyse, een concept, een notitie of een aangepast plan.
   - **Voorbereiden en vragen:** alles wat naar buiten gaat, zoals een post, een e-mail, een wijziging in je CRM of een uitgave, wordt klaargezet en wacht op jouw akkoord.
   - **Bewaren:** nuttige informatie waar nu niets mee hoeft.
   - **Terugsturen:** verkeerde chat; hij gaat met een reden terug naar de router.
5. **Bevestiging.** De chat stuurt één regel terug ("LinkedIn: concept - reactiepost geschreven, wacht op jouw akkoord"). Typ "status?" in de inbox-chat en je ziet per screenshot wat ermee gebeurd is.

## Installeren

Je hebt Claude Code op het web nodig (claude.ai/code) met de claude-code-remote-tools (`send_message`, `get_session`). Cloud-sessies hebben die standaard.

**1. Installeer de plugin**

```
/plugin marketplace add GoldLemonAI/Screenshot-router
/plugin install screenshot-router@goldlemon
```

Of kopieer de twee mappen in `skills/` naar `.claude/skills/` in je eigen repository, en merge dat naar de hoofdbranch zodat nieuwe sessies ze laden.

**2. Voeg je routes toe**

Kopieer `skills/screenshot-router/routes.example.md` naar `.claude/screenshot-routes.md` in de repository waarop je sessies draaien, en vervang de voorbeelden door je eigen chats en hun sessie-ID's. De regels **takes** en **not** zijn het belangrijkst, dus maak ze concreet. Je kunt ook gewoon een screenshot sturen: als er nog geen routebestand is, biedt de router aan er een te maken op basis van je open sessies.

**3. Maak een inbox**

Start één sessie als inbox, bijvoorbeeld "Screenshot inbox", en zet hem vast in de Claude-app op je telefoon. Deel een screenshot vanaf je telefoon in die chat. Tekst erbij is niet nodig; voeg een notitie toe als je iets specifieks wilt.

De skills zelf zijn in het Engels geschreven. Claude volgt ze ook als je in het Nederlands chat, en de router neemt tekst over in de taal waarin hij op de screenshot staat.

## Beperkingen

- Alleen Claude Code-sessies kunnen screenshots ontvangen. Gewone claude.ai-chats en Projects zijn niet bereikbaar.
- De ontvangende chat krijgt een volledige uitwerking van de tekst, niet de afbeelding zelf. Dat werkt goed voor posts, e-mails en dashboards, en minder goed voor feedback op puur visueel ontwerp.
- Van een video krijg je alleen wat in dat ene beeld te zien is.
- Bevestigingen komen als berichten binnen in de inbox-chat; je ziet ze de volgende keer dat je die chat opent.
- Chats die op je eigen computer draaien (Remote Control) pakken een screenshot pas op als die computer aan staat. De router controleert dat vóór het versturen en vraagt je wat je wilt als zo'n chat offline is.
- Er gebeurt niets vanzelf als je een screenshot maakt. Je deelt hem zelf in de inbox-chat.

## Bestanden

| Bestand | Wat het doet |
|---|---|
| `.claude-plugin/` | Plugin- en marketplace-manifest |
| `skills/screenshot-router/SKILL.md` | Lezen, routeren, het overdrachtsbericht, bevestigingen en teruggestuurde screenshots |
| `skills/screenshot-router/routes.example.md` | Voorbeeldroutes; je eigen routes staan in `.claude/screenshot-routes.md` |
| `skills/screenshot-handler/SKILL.md` | Hoe een ontvangende chat beslist en terugmeldt |

## Licentie

MIT. Gemaakt door [GoldLemon](https://www.goldlemon.nl).
