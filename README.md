# The Gilded Ledger

An immersive, noncommercial Sea of Thieves field guide. Static HTML, CSS and modern JavaScript, with Python importers and **no third-party runtime packages, API keys, accounts, or build dependencies**.

- Searchable treasure catalog, trading-company/category filters, bookmarks and item images.
- Item details with **all five standard emissary grades**, preserving minimum–maximum sale ranges and currencies.
- Eight world-event loot pools, including observed quantities, shared pools, evidence labels and possible bonus Chests of Fortune.
- Gold Rush countdown at **01:00–02:00 and 17:00–18:00 UTC**, local time zones, DST handling and a seven-day `.ics` export.
- Dated official announcements and month-bounded recurring company events.
- A local ship’s manifest with quantity controls and separate gold/doubloon totals.

Snapshot reviewed November 10, 2026: **403 catalog entries, 365 sourced price entries, 379 item images**. Remaining gaps are labeled; this is not an exhaustive current-value guarantee.

## Put it straight into GitHub

Upload **the contents of this folder**, including `.github`, into a new repository with a `main` branch. Do not upload the enclosing ZIP or a second nested `the-gilded-ledger` folder.

1. Open **Settings → Pages → Build and deployment → Source → GitHub Actions**.
2. Open **Actions → Refresh data and publish Pages → Run workflow**, or push a commit.
3. The deployment job provides your public URL. Project Pages paths work because all local URLs are relative.

The workflow builds, tests, and deploys. Its daily schedule refreshes SoTDB’s catalog/drop pools and the wiki’s bulk price tables, then commits the data and republishes. The browser rechecks deployed catalog data every 15 minutes. A scheduled workflow may run late, and GitHub disables schedules on inactive public repositories after a period of inactivity. If your repository restricts writes or protects `main`, adapt the refresh commit step to your policy or run the importer locally.

Individual item sources are **not refreshed daily by default** to avoid flooding the wiki. Enable repository variable `REFRESH_ITEM_DETAILS=true` for a weekly individual-source refresh. Those requests run sequentially and stop on HTTP 429. Prices retain their own source review dates, independent of the catalog timestamp.

## Run locally

Requires Python 3.9+; Node 20+ is needed only to run the JavaScript tests.

```sh
python3 -m http.server 4173 --bind 127.0.0.1
```

Open <http://127.0.0.1:4173>. Serve over HTTP; double-clicking `index.html` does not allow browsers to fetch JSON reliably.

```sh
npm test
npm run build
```

Or run the commands directly:

```sh
node --test tests/*.test.js
python3 -m unittest discover -s tests -p 'test_*.py'
python3 scripts/build.py
```

`dist/` is the deployment output. It works on any static host. No install step is required.

## Refresh and maintain data

```sh
python3 scripts/sync_data.py --images
python3 scripts/enrich_data.py --images
# Optional: recheck all individual source prices, respectfully and sequentially.
python3 scripts/enrich_data.py --refresh --images
```

`sync_data.py` reads public SoTDB endpoints and wiki bulk tables. `enrich_data.py` reads item infoboxes, matches names without fuzzy guessing, caches successful source HTML locally, and downloads missing item images where available. An upstream denial or rate limit preserves the last known values; a severe catalog coverage drop aborts the refresh. It never replaces unknown values with zero. Inspect logs for unavailable sources.

- `data/catalog.json`: display catalog, source-reported event relationships, and per-item price evidence.
- `data/prices.json`: normalized-name price index with source URLs and individual review dates.
- `data/calendar.json`: manually reviewed official announcements and dated recurring rules.
- `scripts/`: dependency-free importers and static build.
- `tests/`: payout, UTC/DST, currency separation, source parsing and event-reference checks.

**Calendar announcements need human review.** The official Events Hub blocks automated requests in this environment, so the project does not pretend to import every new announcement automatically. Edit `data/calendar.json` from official announcements, update `reviewedAt`, and commit. Expired events are hidden; the UI warns when the calendar review is over seven days old. Date-only announcements are labeled and never presented as precise live activation windows. Gold Rush follows a published recurring rule and can be suspended during multiplier events; the timer cannot detect that server-side state.

The Season 21 Chest of Fortune override is reviewed against official patch 3.9.0. Review `scripts/sync_data.py` when new patch notes change world-event rules. The source’s older guaranteed-looking Chest of Fortune relationships are replaced by the official chance-based rule.

## Accuracy and scope

This is a **dated community reference**, not an official game API or a connection to the player’s server. The catalog includes quest objects, historical items and cosmetics as well as sellable treasure. Coverage is shown in the app; missing prices and images are explicitly labeled. Fish, meat, animal variants, every buyer-specific price adjustment and every conditional turn-in are not exhaustively covered. Explicit commodity conditions and alternative currencies are shown separately. Ambiguous infobox prices are kept unconfirmed instead of being guessed.

Emissary tables calculate standard-company estimates of 1×, 1.33×, 1.67×, 2× and 2.5× for eligible sales. Guild rates differ. Gold Rush and other temporary multipliers are excluded from these tables. Cargo condition, item charge, buyer choice, alliance shares and special turn-ins may change the payout. Evidence labels on loot pools describe the upstream source, not verification by this project; quantities and group alternatives must not be added up as a guaranteed event payout.

## Privacy

Bookmarks, haul quantities and time zone are stored only in this browser’s local storage. There are no tracking scripts, sign-ins or analytics. Most item images and both fonts are bundled. A missing image may retain an upstream URL, so that host sees a normal image request. External sources open only when followed.

## Credits and licensing

Application code: MIT. Game imagery and source-derived data are **not** covered by the code license. This is a noncommercial fan project; read [THIRD_PARTY.md](THIRD_PARTY.md) before reuse or monetization. Sea of Thieves and its imagery belong to Microsoft / Rare. The project is not affiliated with or endorsed by either.
