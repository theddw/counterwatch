# Counterwatch

A mobile-first Overwatch counter-picker. Enter the enemy team and your team, choose the
role you want to play, and it ranks the best heroes for that role with an explanation of *why*.

Single self-contained file — `index.html`. No build step, no dependencies, works offline.

## Run it on your iPhone (Netlify drag-drop)

1. Go to <https://app.netlify.com/drop>
2. Drag the **`counterwatch` folder** onto the page (it only needs `index.html`).
3. Netlify gives you a URL like `https://something.netlify.app`.
4. Open that URL in **Safari** on your iPhone → Share → **Add to Home Screen**.
   It launches full-screen like a native app.

> The `.claude/` folder is only for local preview — you can delete it before uploading, or
> just leave it; Netlify ignores it.

## How the recommendations work

The "MATCH" score blends three transparent layers, and every point shows up as a bullet
reason so you can see the logic and overrule it:

1. **Counters** — a rules engine over each hero's **kit archetype** (tags like `airborne`,
   `dive`, `antidive`, `barrier`, `antiheal`, `longrange`) plus a curated list of iconic
   hard-counters (`ENEMY_COUNTERS` / `CANDIDATE_COUNTERS`).
2. **Map** — pick a map and its type (sightlines / chokes / verticality / flank routes)
   nudges picks (e.g. snipers up on Circuit Royal, brawl/CC up on King's Row).
3. **Meta** — each hero carries its **Season 3 tier + win-rate + pick-rate** (from
   counterwatch.gg / Blizzard rates), shown on every card and folded in as a tiebreaker.

> No source publishes a full hero-vs-hero win-rate *matrix* — that part is kit-design
> knowledge. The tier/win/pick numbers are the published hard data and are cited in-app.

## 📷 Photo scan (Google Gemini)

Tap **Scan screenshot** to auto-fill both teams from a screenshot (best: the in-match
scoreboard / Tab view, which shows both teams). The image is resized in-browser, sent to
Google Gemini with our exact roster + map list so it normalizes to our spelling, then heroes
are slotted by role. A **⇄ Swap** button flips which team is enemy vs. yours; any name it
couldn't match is flagged so you can set it by hand.

**Setup:** tap ⚙ → paste a free Gemini key from <https://aistudio.google.com/apikey>.

- The key is stored **only in this browser** (`localStorage` keys `cw_gem_key` / `cw_gem_model`)
  — never in the repo. Set a usage limit in Google AI Studio and rotate the key if it leaks.
- Default model `gemini-2.5-flash` (editable). Gemini's free tier covers personal use; cost
  otherwise is a fraction of a cent per scan.
- **Caveat:** vision models don't reliably recognize the brand-new 2026 heroes by portrait —
  verify those slots. Scan is a fast first pass; the picker is the source of truth.
- **If a scan shows a CORS / network error:** the browser→Gemini direct call was blocked on
  your network. Fix is a tiny serverless proxy (Cloudflare Worker) holding the key — ask and
  it's ~20 lines. Everything else in the app keeps working regardless.

## Updating it as patches land

All data lives at the top of the `<script>` in `index.html`:

- **`HEROES`** — add/edit a hero, its `tags`, and its `meta:{tier,wr,pr}`. New 2026 heroes
  are flagged `new:true` (tags from early kit info — refine as the meta settles).
- **`MAPS`** — add/edit a map, its `mode`, and its `tags` (`longsight`, `open`, `brawl`,
  `chokes`, `vertical`, `flanks`, `pits`).
- **`ENEMY_COUNTERS`** — "this enemy hero hard-counters these picks" (adds a warning).
- **`CANDIDATE_COUNTERS`** — "this pick hard-counters these enemies" (adds a bonus + reason).

Refresh the win-rates each season from <https://www.counterwatch.gg/stats/overwatch/tier-list>.
Save the file, re-upload to Netlify (or drag again to a new Drop), done.

> Heads-up: `counterwatch.gg` is a real existing stats site — a name collision with this app.
> Fine for personal use; rename the wordmark in `index.html` if you ever share it.

## Local preview

`python -m http.server 5050` in this folder, then open `http://localhost:5050`.
