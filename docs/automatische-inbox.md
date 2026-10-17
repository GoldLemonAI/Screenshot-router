# Automatische screenshot-inbox instellen

[English summary in the README](../README.md#automatic-mode)

Met deze instelling hoef je een screenshot niet meer zelf te delen. Je telefoon zet elke nieuwe screenshot in een Google Drive-map, en je inbox-chat kijkt daar elk uur in.

```
screenshot op je telefoon
      │  sync-app uploadt automatisch
      ▼
Drive-map "Screenshot inbox"
      │  elk uur: geplande taak in je inbox-chat
      ▼
screenshot-router ── leest de afbeelding, slaat privé over,
      │              controleert of de chat online is
      ▼
juiste chat ── doet er iets mee en meldt terug
      │
Drive-map "Verwerkt" ── zodat niets twee keer gaat
```

Reken op een half uur voor de eerste keer.

## Wat je nodig hebt

- Claude Code op het web (claude.ai/code) met de Google Drive-connector aan.
- De plugin of de skills uit deze repo, en een ingevuld `.claude/screenshot-routes.md` (zie de README).
- Een Android-telefoon. Op een iPhone kan het niet volledig automatisch; zie [iPhone](#iphone).

## Stap 1: maak twee mappen in Google Drive

1. Maak in Google Drive een map **Screenshot inbox**.
2. Maak daarin een submap **Verwerkt**.
3. Open elke map in je browser en kopieer het laatste deel van de adresbalk, na `/folders/`. Dat is de map-ID. Je hebt er twee nodig.

Je kunt dit ook aan je inbox-chat vragen: "Maak in mijn Drive een map Screenshot inbox met een submap Verwerkt en geef me de ID's."

## Stap 2: zet de map-ID's in je routebestand

Voeg dit toe onderaan `.claude/screenshot-routes.md`:

```
## drive-inbox
- **inbox folder**: <ID van Screenshot inbox>
- **done folder**: <ID van Verwerkt>
```

Commit en push het bestand, zodat de inbox-chat het leest.

## Stap 3: laat je telefoon screenshots uploaden

De Google Drive-app zelf synchroniseert geen mappen. Je hebt een sync-app nodig, bijvoorbeeld **Autosync for Google Drive** of **FolderSync** uit de Play Store.

1. Installeer de app en log in met hetzelfde Google-account als je Drive-connector.
2. Maak een nieuwe koppeling (heet vaak "folder pair"):
   - **Map op je telefoon:** de screenshotmap. Meestal `Pictures/Screenshots`; op Samsung `DCIM/Screenshots`.
   - **Map in Drive:** `Screenshot inbox`.
3. Kies als methode **alleen uploaden** (upload only).
   Kies niet "tweerichtingen" of "spiegelen". Dan verwijdert de app je screenshots van je telefoon zodra de router ze naar Verwerkt verplaatst.
4. Zet uploaden via mobiele data aan als je niet wilt wachten op wifi.
5. Sta toe dat de app op de achtergrond draait (batterijbesparing uit voor deze app), anders stopt hij na een tijdje.
6. Maak een test-screenshot en kijk of hij binnen een paar minuten in de Drive-map staat.

## Stap 4: maak de geplande taak

Open je inbox-chat op claude.ai/code en vraag:

> Maak een routine die elk uur tussen 8:00 en 22:00 (Nederlandse tijd) in deze chat het bericht "Check de screenshot-inbox" stuurt.

Elke ronde doet de router dan dit:

1. Hij zoekt nieuwe afbeeldingen in Screenshot inbox. Zijn er geen, dan zegt hij dat in één regel.
2. Hij leest elke screenshot als tekst via Google Drive.
3. Hij slaat privé-screenshots over: bank, inlogcodes, wachtwoorden, medische zaken, appjes met familie, persoonlijke foto's. Twijfelt hij, dan slaat hij over en noemt hij het in de samenvatting.
4. Hij stuurt de rest naar de juiste chat, met een link naar de originele afbeelding.
5. Hij verplaatst elke verwerkte screenshot naar Verwerkt. Mislukt het versturen, dan blijft hij staan en probeert de router het de volgende ronde opnieuw.
6. Hij sluit af met een korte samenvatting per screenshot.

Per ronde gaan er hooguit 10 screenshots door; de rest volgt in de ronde erna.

## Stap 5: zorg dat je andere chats berichten aannemen

Dit is de stap die het vaakst misgaat.

- **Chats op je eigen computer** (Remote Control) pakken een bericht alleen op als die computer aan staat en Claude daar draait. Staat hij uit, dan wacht het bericht. De router meldt dat in de samenvatting.
- **Permissiemodus.** Hebben de inbox-chat en de ontvangende chat een andere permissiemodus, dan wordt een binnenkomend bericht tegengehouden tot je het in de ontvangende chat goedkeurt. Keur je het niet op tijd goed, dan vervalt het en komt het nooit aan. Opnieuw sturen helpt niet zolang de modi verschillen. Zet daarom de inbox-chat en alle chats die screenshots ontvangen op **dezelfde** modus. Je ziet en wijzigt de modus onder het invoerveld van elke chat.
  - Kies liever **auto** dan **bypass permissions**. De inbox leest tekst van willekeurige screenshots, en daar kan tekst in staan die probeert een agent iets te laten doen. In auto-modus controleert Claude riskante acties nog; in bypass gebeurt alles zonder controle.

## Stap 6: test het hele pad

1. Maak een screenshot van een LinkedIn-post.
2. Wacht tot hij in Drive staat.
3. Typ in je inbox-chat "Check de screenshot-inbox" om niet op het hele uur te wachten.
4. Je ziet in welke chat hij terechtkwam. Open die chat en kijk wat hij deed.
5. Typ daarna "status?" in je inbox-chat. Je ziet dan per screenshot de bevestiging, of "nog geen bevestiging".

## Problemen oplossen

| Wat je ziet | Waarschijnlijke oorzaak | Oplossing |
|---|---|---|
| Screenshot komt niet in Drive | Sync-app gestopt of alleen op wifi | Batterijbesparing uit voor de app, mobiele data aan |
| "Geen nieuwe screenshots" terwijl er wel een staat | Verkeerde map-ID in het routebestand | Controleer de ID's uit stap 1 en 2 |
| Router zegt dat de Drive-connector ontbreekt | Connector niet aan voor de inbox-chat | Zet Google Drive aan bij de connectors van claude.ai |
| Bericht verstuurd, maar de chat doet niets | Computer uit, of bericht wacht op goedkeuring | Zet je computer aan; gebruik overal dezelfde permissiemodus (stap 5) |
| In de ontvangende chat staat "not approved before expiry" | Permissiemodi verschillen | Zelfde modus in alle chats (stap 5), daarna opnieuw sturen |
| Screenshots verdwijnen van je telefoon | Sync-app staat op tweerichtingen | Zet de koppeling op "alleen uploaden" |

## iPhone

iOS heeft geen automatisering die start zodra je een screenshot maakt. Wat wel kan:

- Deel de screenshot via het deelmenu naar Google Drive, map Screenshot inbox. Dat is één extra tik; de rest gaat automatisch.
- Of deel hem direct in je inbox-chat in de Claude-app, zoals zonder automatische modus.

## Privacy

Met deze instelling komen al je screenshots in Google Drive, ook privé. De router stuurt privé-screenshots niet door, maar ze staan wel in je Drive. Wil je dat niet, gebruik dan een aparte screenshotmap voor werk (sommige telefoons laten je dat instellen), of deel screenshots met de hand.
