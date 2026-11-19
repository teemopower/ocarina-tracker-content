# Ocarina Tracker — checklist content

Public checklist content for **Ocarina Tracker**, the unofficial fan checklist
app for the Ocarina of Time remake (Switch 2). The app downloads these files
from this repo (served by GitHub Pages) every time it launches, so **editing
a file here updates the checklist inside the app — no app-store update
needed.**

- Live content: https://teemopower.github.io/ocarina-tracker-content/content/manifest.json
- App repo: https://github.com/teemopower/ocarina-tracker (private)

## How it works

1. On launch the app fetches `content/manifest.json`.
2. If the manifest `version` is newer than the one on the phone, the app
   downloads every list file, checks them, and switches to the new content.
3. If the phone is offline, the download fails, or a file looks wrong, the
   app keeps using the checklist it already has (the one built into the app
   as a fallback). The checklist is never empty or broken because of a bad
   update.

## Files

```
content/
  manifest.json            — version number, update date, list of files + counts
  skulltulas.json          — Gold Skulltulas (100)
  heart-pieces.json        — Heart Pieces (36)
  bottles.json             — Bottles (5)
  masks.json               — Masks (8)
  trading-quest.json       — Trading Quest (11)
  fairy-thoughts.json      — Fairy Thoughts (23)
  fountains.json           — Great Fairy Fountains (6)
  hall-of-fallen-foes.json — Hall of Fallen Foes (9 bosses, 41 enemies, 3 trials)
```

## The data format

### A list file (e.g. `fairy-thoughts.json`)

```json
{
  "id": "fairy",
  "title": "Fairy Thoughts",
  "subtitle": "23 thoughts · new in the remake",
  "icon": "sparkles",
  "items": [ ... ]
}
```

- `id` — short stable id for the list. Never change it once published.
- `title` — the name shown in the app.
- `subtitle` — one line under the title, e.g. `23 thoughts · new in the remake`.
- `icon` — one of: `skull`, `heart`, `flask`, `smile`, `route`, `sparkles`,
  `swords`, `flame`. (Only these — a new icon needs an app update first.)
- `items` — the checklist entries, in the order they appear in the app.

### One checklist item

```json
{
  "id": "fairy-01",
  "title": "Rock west of the Castle Town entrance",
  "region": "Hyrule Field",
  "hint": "Just outside the Castle Town gate, a glowing mark sits on a rock to the west. ...",
  "era": "child",
  "needs": "Hookshot",
  "nightOnly": true,
  "remakeNote": "Brand-new in the Switch 2 remake."
}
```

- `id` (required) — unique across **all** lists, e.g. `skulltula-42`,
  `heart-17`, `fairy-09`. **Never reuse an id, and never rename one that
  is already published** — players' checkmarks are saved by id, so renaming
  one would un-check it for everyone.
- `title` (required) — short spot name.
- `region` (required) — in-game map area, e.g. `Kokiri Forest`.
- `hint` (required) — how to reach/collect it, in your own words.
- `era` (required) — `child`, `adult`, or `both` (either age can collect it).
- `needs` (optional) — item, song, or gear needed, e.g. `Hookshot`.
- `nightOnly` (optional) — `true` when it only appears at night.
- `remakeNote` (optional) — what changed vs the 1998 original, if anything.

### The Hall file (`hall-of-fallen-foes.json`)

```json
{
  "id": "hall",
  "title": "Hall of Fallen Foes",
  "subtitle": "Boss rematches, enemy statues & trials",
  "bosses": [ { "id": "boss-gohma", "name": "Gohma", "location": "Great Deku Tree", "hint": "...", "era": "child" } ],
  "enemies": [ { checklist item }, ... ],
  "trials": [ { checklist item }, ... ]
}
```

Bosses use `name` + `location` instead of `title` + `region`. Enemies and
trials are normal checklist items.

### The manifest (`content/manifest.json`)

```json
{
  "version": 1,
  "updatedAt": "2026-11-19",
  "totalItems": 242,
  "lists": [
    { "id": "skulltulas", "title": "Gold Skulltulas", "file": "content/skulltulas.json", "count": 100 }
  ],
  "hall": { "id": "hall", "title": "Hall of Fallen Foes", "file": "content/hall-of-fallen-foes.json", "bosses": 9, "enemies": 41, "trials": 3, "count": 53 }
}
```

- `version` — bump by **1** on every content change. The app only picks up
  a change when this number goes up.
- `updatedAt` — the date of the change, `YYYY-MM-DD`.
- `count` — the number of items in that file. It must match the file, or
  the app rejects the update.

## How to change the content

### Fix a typo or reword a hint

1. Open the list file (e.g. `content/fairy-thoughts.json`) and edit the text.
2. In `content/manifest.json`, bump `version` by 1 and set `updatedAt` to
   today's date. Do **not** change any `count`.
3. Commit and push. GitHub Pages publishes the change in about a minute.
4. The app picks it up on its next launch (or from About → Check for
   content updates).

### Add a new item

1. Add the item to the right list file, with a **new unique id** (next free
   number for that list, e.g. `fairy-24`).
2. In `content/manifest.json`, bump `version` by 1, set `updatedAt` to
   today, and update that list's `count` **and** `totalItems`.
3. Also update the list's `subtitle` if it states a number
   (e.g. `23 thoughts` → `24 thoughts`).
4. Commit and push.

### Add a whole new list

1. Create a new file, e.g. `content/new-list.json`, in the list-file format
   above, with a new unique `id`.
2. In `content/manifest.json`, add it to `lists`, bump `version` by 1, set
   `updatedAt` to today, and update `totalItems`.
3. Commit and push. (The list appears in the app automatically.)

### Remove an item

Prefer to keep it: deleting an entry also deletes that checkmark for
everyone who had it checked. If it really must go, delete it from its file,
then bump `version`, set `updatedAt`, and fix the `count` and `totalItems`
in the manifest.

## Before you push — check the JSON is valid

A single missing comma breaks the whole update (the app will keep the old
content, but nobody gets the fix). Check each file you edited:

```bash
python3 -m json.tool content/fairy-thoughts.json > /dev/null && echo OK
python3 -m json.tool content/manifest.json > /dev/null && echo OK
```

Or paste the file into any online JSON validator.

## Rules for the content

- **Your own words only.** Every hint is written from scratch. Never copy
  text from a guide, wiki, or walkthrough.
- **No Nintendo trademarks, art, screenshots, or music.** Place and item
  names are used only as factual identifiers so players can find them.
- **No spoilers beyond the checklist.** Hints say where something is and
  how to get it — nothing more.
- **Ids are forever.** Never reuse or rename a published id.
- **Counts must match.** If a file has 24 items, its manifest `count` must
  say 24 and `totalItems` must include them.
