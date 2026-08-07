# ✈️ SkyDuels — Aerial Card Combat

A self-contained, browser-based **collectible card battler** themed around real-world (and fictional) aircraft. Build a deck of planes, deploy them to your front line, and battle friends or an AI opponent in real time.

The entire game lives in a single file: **`v1.0.0/index.html`** — no build step, no framework, no backend server of your own to run. All multiplayer state is synced through ** Firebase Realtime Database**.

---

## 🎮 Features

- **Real-time multiplayer** via Firebase (anonymous sign-in, no account needed).
- **AI opponent** — if no human joins within ~5 seconds, a balanced AI player is auto-added so you can always play solo.
- **Game modes**
  - `5v5` — Standard duel (5 action + 10 backup cap).
  - `7v7` — Extended clash (7 action + 10 backup cap).
  - `🌀 Chaos Dungeon` — Free-for-all for 4+ players.
- **Card combat** on a 5-slot front line:
  - **Deploy** planes from your Backup / Action rows to the Front.
  - **Attack** the enemy front line.
  - **Use Skill** — every plane has one of 27 skills (offensive, defensive, or fusion).
  - **Action cards** — single-use buff / debuff / ban effects.
- **Rarity tiers**: Common · Rare · Super Rare · Mythic · Legendary (derived from card type & stats).
- **Shop** — buy single cards, card packs, and blind boxes using **SDC** currency.
- **🏪 Marketplace** — sell your own cards to other players and browse their listings; **SkyStock** shows each card's resale value (70% of original price).
- **🎴 Lineup editor** — save a starting Action + Backup lineup that auto-loads each match.
- **🛠️ Admin console** (password-protected) — create cards, manage store products, and view all players.
- **🌏 Language toggle** — switch between **English** and **中文**.
- **🎁 Gift code** redemption for bonus SDC.
- **AI-generated card artwork** rendered on the battlefield.

---

## 🧱 Tech Stack

| Layer | Technology |
|-------|------------|
| UI | Plain HTML + CSS (glassmorphism style), no framework |
| Logic | Vanilla JavaScript (ES modules) |
| Backend | Firebase — **Anonymous Auth** + **Realtime Database** |
| Loading | `importmap` pulling Firebase 12.x from the gstatic CDN |
| Data | Card definitions are bundled in `SHEET_CARDS` and synced into `admin/cards` in Firebase |

> The Firebase project config and the admin password are **embedded directly in `index.html`** (near the top of the `<script type="module">` block). See [Configuration & Security](#️-configuration--security) before deploying publicly.

---

## 🚀 How to Run

1. **Internet connection is required** — the page loads Firebase from a CDN and reads/writes the live database.
2. Open the game in a modern browser. Two options:
   - **Simplest:** double-click `v1.0.0/index.html` (works in current Chrome/Edge/Safari/Firefox thanks to import maps).
   - **Recommended:** serve it locally so module loading is most reliable:
     ```bash
     cd SkyDuels/v1.0.0
     python3 -m http.server 8000
     # then open http://localhost:8000
     ```
3. Wait for the status badge to show **🔗 Connected**. You are signed in anonymously and start with **150 SDC**.

### Playing with friends
- Click **⚔️ Play!** to create a public room, or **Create Room** and share the generated room code.
- A friend chooses **5v5 / 7v7 / Chaos**, then enters your code in **Join** to drop into the same room.
- If nobody joins within ~5 seconds, an AI opponent is added automatically.

---

## 📖 How to Play

1. **Get cards.** Open the **🛒 Shop** (or **🏪 Marketplace**) and spend SDC on cards/packs. New players start with 150 SDC but an empty deck — buy a few planes first.
2. **Set a lineup (optional).** Open **🎴 Lineup** and pick which Action and Backup cards start each match, then **💾 Save Lineup**.
3. **Start a match.** Pick a mode in the lobby and hit **⚔️ Play!**
4. **On your turn:**
   - Tap a card in **⑤ Backup** or **⑥ Action** to select it, then tap an empty **④ Front** slot to **Deploy** it (uses your turn).
   - Tap a front-line plane, then **⚔️ Attack** an enemy card (uses your turn).
   - Tap an **Action** card to apply its one-time **Buff / Debuff / Ban** effect (uses your turn).
   - Tap a front-line plane and **✨ Use Skill** to trigger its numbered skill.
   - The turn auto-ends after each action; when it's the AI's/opponent's turn the controls are locked.
5. **Win** by destroying all opposing front-line and backup cards. Use **🔄 Restart** to re-deal, or **Leave** to exit.

> **Skill numbers (1–27)** map to distinct effects (direct damage, shields, heals, debuffs, and the two **dragon fusion** skills 22/23). Special cards like `東方巨龍` / `西方巨龍` combine via fusion.

---

## 🗂️ Project Structure

```
SkyDuels/
├── v1.0.0/
│   ├── index.html      # The entire game (UI + logic + Firebase wiring)
│   └── README.md       # This file
├── SkyDuels_civilian_fill.csv   # Source data for civilian aircraft cards
└── SkyDuels_new_rows.csv         # Additional card data rows
```

The game is a single self-contained `index.html`. The CSV files at the project root are the data sources that feed the bundled `SHEET_CARDS` list (aircraft names, HP, DMG, skill, type).

---

## 🛠️ Admin Console

1. Click **⚙️ Admin** and enter the admin password when prompted.
2. From there you can:
   - **Create Card** — define name, HP, DMG, skill (1–27), rarity, and type (Plane / Action).
   - **Manage Store Products** — add single cards, card packs, or blind-box packs to the Shop with price & quantity.
   - **Sell Action Cards** — list any action card on the Shop at a chosen price.
   - **👥 Players** — see everyone who has ever played, with online status.

> The Admin console is **locked during an active battle** so rules can't be changed mid-fight.

---

## ⚠️ Configuration & Security

- **Firebase config** and the **admin password** are hard-coded inside `index.html`. Anyone who opens the file can read them.
  - Before any public deployment, move secrets out of the client (or at least lock down **Firebase Realtime Database security rules**) so anonymous users cannot read/write arbitrary data.
  - Treat the admin password as public once the file is shared.
- **Privacy:** players are authenticated **anonymously**; only a random UID, display name, currency, and presence flag are stored.
- **Reset Progress** (lobby button) permanently wipes your owned cards and resets currency to 150 SDC.

---

## 📝 Notes & Known Trade-offs

- Card artwork is AI-generated and loaded by card id; broken images fail silently.
- The AI is intentionally tuned to be beatable (fixed Lv.1, Common/Rare planes only, sometimes skips attacks).
- All game state (rooms, players, cards, shop, market listings) lives in the shared Firebase project, so it is global across all players using this build.
