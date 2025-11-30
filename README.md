### Full Youtube Guide

[![Watch the video](https://img.youtube.com/vi/gZmlPXZjs9s/0.jpg)](https://www.youtube.com/watch?v=gZmlPXZjs9s)

# 🎬 Taste Matcher – Letterboxd + TMDb Watchlist Ranker

Taste Matcher is a backend-first Node.js project that:

- Reads your **Letterboxd ratings CSV**
- Reads your **Letterboxd watchlist CSV**
- Removes **rewatches** (movies appearing in both files)
- Builds a **multi-dimensional taste model** using TMDb metadata:
  - genres
  - directors & writers
  - countries & regions
  - decades / eras
  - keywords (mood / tone / themes)
  - collections (franchises / sagas)
- Blends that with a **nearest-neighbour similarity layer** (films most similar to what you already love)
- Predicts how much you'll like every film in your watchlist
- Ranks the entire watchlist from **most-likely-to-enjoy → least**

Includes a clean UI with:

- ⭐ Rewatch list (posters + your rating)  
- ⭐ Genre profile (clickable, expands into films per genre)  
- ⭐ Ranked watchlist (full cards + match percentage)

Everything runs **locally**, no frontend frameworks, no database.

---

## 🧱 Tech Stack

- **Node.js + Express** (backend)
- **TMDb API** (movie/TV metadata)
- **csv-parse** (Letterboxd CSV reading)
- **HTML + CSS + vanilla JS** (frontend)
- **Disk caching** (no repeated TMDb calls)

---

## 📁 Project Structure

`taste-matcher/
├─ data/
│  ├─ ratings.csv        # your ratings (Letterboxd export)
│  └─ watchlist.csv      # your watchlist or any list export (renamed)
├─ public/
│  ├─ css
   │  └─ main.css
   │  ├─ index.html         # UI (HTML + JS + CSS, or imports)
│  ├─ js
   │  └─ app.js
├─ cache/
│  ├─ tmdb_cache.json    # TMDb responses saved here
│  ├─ derived_cache.json # taste model + recommendations + rewatch ranking
│  ├─ hidden_rewatches.json # rewatches you chose to hide from ranking
│  └─ data_state.json    # persisted ratings + watchlist + overlap state
├─ exported/
│  └─ ranked-watchlist-*.csv # Letterboxd-ready list exports
├─ server.js             # backend logic
├─ .env                  # TMDb API key + PORT
└─ package.json`



> ⚠️ IMPORTANT:  
> Do NOT commit `.env`, `/data/*`, or `/cache/*` to a public GitHub repo.

---

## 1️⃣ Requirements

- Node.js **18+**
- TMDb **Developer API Key**
- Letterboxd:
  - `ratings.csv`
  - `watchlist.csv`

---

## 2️⃣ Getting Your Letterboxd CSV Files

Place your CSVs inside:

`taste-matcher/data/`

### 🔹 Ratings Export (unchanged)
1. Letterboxd → **Settings → Data → Export**
2. Download **ratings.csv**
3. This will usually not be the case, but in case the name of the file is **not** `ratings.csv`, rename the file to `ratings.csv`.

### 🔹 Watchlist Export (now fully universal)
Taste Matcher accepts **any** Letterboxd-generated list CSV:

- “Watchlist → Export” (official format)
- Any custom list export
- Old/legacy list formats

Just rename your chosen file to: `watchlist.csv`

The .csv file can be any of your lists, it could also be your watchlist, but when you are putting the file into `taste-matcher/data/`, **make sure** the .csv's file name is renamed to `watchlist.csv`.

Then click **Reload Watchlist** inside the UI to apply your new list instantly.

---

## 3️⃣ Setting Up TMDb API Key

Go to:

https://www.themoviedb.org/settings/api

Generate an API key → V3 authentication.

Edit `.env`:

TMDB_API_KEY=YOUR_KEY_HERE
PORT=3000


> `.env` is already included. You only fill your key.

---

## 4️⃣ Install Dependencies (Important)

Make sure you are inside the project folder:
cd taste-matcher


### 4.1 Install Node dependencies

Run:
```npm init -y```
```npm install express axios csv-parse dotenv```


This will install everything listed inside `package.json`, including:

- express  
- axios  
- csv-parse  
- dotenv  

If this completes without errors, you're good.

---
### 4.2 Verify installation

Run:

node -v
npm -v


You should see:

- Node version **18 or higher**
- Any npm version is fine

---

## 5️⃣ Start the Backend


### On first run:
- Loads CSVs  
- Calls TMDb (movie/TV search + details)  
- Builds genre profile  
- Builds ranked watchlist  
- Saves everything to `/cache/`  

### On later runs:
- Loads from disk cache  
- Zero API calls  
- Instant startup  

---

## 6️⃣ Open the UI

Open: http://localhost:3000/


You will see:

---

## ⭐ Rewatches (Removed From Ranking)

Movies found in both ratings + watchlist.

Each card shows:

- Poster  
- Title + Year  
- Genres  
- TMDb rating  
- **Your rating**  
- Link to Letterboxd  

These are **excluded from recommendations**, but their ratings are **included** in taste calculation.

---

## ⭐ Interactive Features (Live Editing, Instant Re-Ranking)

Taste Matcher now supports full **live mutation** of your data — no need to re-export CSVs unless you want to.

### ✅ Add a new rating (manual)
Supports:
- Title  
- Year (optional)  
- Letterboxd URL (optional)  
- **TMDb URL or ID (recommended — auto-fills title & year)**  
- Your rating  

The backend:
- Resolves TMDb metadata (only once — cached)
- Prevents duplicate ratings
- Updates the taste model
- Re-ranks both watchlist + rewatches instantly  
- Never re-calls TMDb for previously seen films

### ✅ Add a film to your watchlist (manual)
Supports:
- Title  
- Year  
- Letterboxd URL  
- **TMDb URL/ID for instant, accurate metadata**

The backend:
- Rejects duplicates
- If it’s already rated → automatically moves it to **rewatches**
- Otherwise adds it to the watchlist
- Updates ranking without extra TMDb calls

### ✅ Mark a film as “Watched”
For any film in your watchlist:
- Removes it from the watchlist  
- Prompts for a rating  
- Adds/updates the rating  
- Moves it to the **rewatch list**  
- Recomputes taste model + rankings instantly

### ✅ Ranked Rewatches (new feature)
Rewatches are ranked by:
 `rewatchScore = 0.6 * userRatingNorm + 0.4 * predictedScore `
 
This gives you a **priority list of films you're most likely to enjoy rewatching**, blending:
- your original rating  
- your taste model  
- film metadata  

### ⚡ TMDb metadata is fetched only once
All metadata — genres, directors, writers, keywords, collections, etc. — is cached permanently.  
Future operations reuse this cached data and never touch TMDb again.


## ⭐ Genre Profile (Interactive)

Shows:

- Average rating you give each genre  
- Number of films in that genre  
- Visual preference bars  

**Click a genre card → expands into all films you rated in that genre.**

Includes posters + your rating.

---

## ⭐ Ranked Watchlist

Your entire watchlist is ranked using a full **multi-factor taste model**, enriched with TMDb data and your entire rating history.

### What influences your ranking:
- **Genre affinity** – how much you like its genres  
- **Director affinity** – how much you like the directors’ other work  
- **Writer affinity** – how much you like their screenwriters  
- **Keyword affinity** – mood/theme/tone similarity using TMDb keywords  
- **Country & region affinity** – how highly you rate films from similar film cultures  
- **Decade affinity** – taste for specific eras (70s, 90s, 2010s, etc.)  
- **Collection affinity** – franchise/universe you already enjoy  
- **Neighbour similarity** – how close this film is to your highest-rated films (k-NN over genres/keywords/era/regions/directors)  
- **TMDb community rating** – normalized quality baseline  


These are normalized (0–1), weighted, and turned into a **predictedScore** for every watchlist film.

## ⭐ Export Ranked Watchlist to Letterboxd

Once your watchlist is ranked, you can export it as a **Letterboxd list CSV** that can be imported straight into a new List on Letterboxd.

From the UI:

- Click **“Export Ranked Watchlist → Letterboxd”**
- You’ll be asked whether to **include rewatches** in the exported list:
  - **Include rewatches** → watchlist items + rewatches are ranked together
  - **Don’t include rewatches** → only watchlist items are exported
- The export only affects the CSV file – the on-page UI stays the same.

The backend creates:

- `exported/ranked-watchlist-YYYY-MM-DDTHH-MM-SS.csv`

in the project folder, using Letterboxd’s **“list export v7”** format, so you can:

1. Create a new list on Letterboxd  
2. Use their “Import” feature  
3. Upload the generated CSV  
4. Get your Taste Matcher ranking as a Letterboxd list in one go


### UI includes:
- Poster  
- Title + year  
- Genres  
- TMDb rating  
- User-genre score  
- Multifactor model score  
- Match percentage (color-coded bar)  
- Rank number  

## 7️⃣ API Endpoints (Full CRUD + Hot Reloading)

| Route | Method | Description |
|-------|--------|-------------|
| `/api/ratings` | GET | Raw ratings list |
| `/api/watchlist` | GET | Watchlist after removing rewatches |
| `/api/overlap` | GET | Full rewatch list (with metadata) |
| `/api/genre-profile` | GET | Multi-dimensional taste model (genres, people, regions, decades, keywords, collections) |
| `/api/genre-titles/:id` | GET | Rated films filtered by genre |
| `/api/recommendations` | GET | Ranked watchlist (multi-factor taste model) |
| `/api/rewatch-ranking` | GET | Ranked rewatch list |
| `/api/mark-watched` | POST | Remove from watchlist → add/update rating, move to rewatches |
| `/api/add-rating` | POST | Add a rating (TMDb URL/ID supported, duplicate-safe) |
| `/api/add-to-watchlist` | POST | Add a film to watchlist (rewatch-aware, uses TMDb if provided) |
| `/api/remove-from-watchlist` | POST | Permanently remove a title from the current watchlist snapshot |
| `/api/hide-rewatch` | POST | Hide a rewatch so it never appears in ranked rewatches |
| `/api/reload-watchlist` | POST | Reload only `watchlist.csv` (keeps ratings, recomputes overlap + rankings) |
| `/api/reset-state` | POST | Reset ratings/watchlist/overlap back to the original CSV baseline |
| `/api/export-ranked-watchlist` | GET | Export a ranked Letterboxd list CSV (optionally include rewatches) |


### 🔁 Hot Reload Watchlist (New Feature)
- Replace `data/watchlist.csv` with any Letterboxd export  
- Press “Reload Watchlist” in the UI  
- The backend:
  - Parses *any* watchlist/list CSV format  
  - Recomputes overlap (rewatches)  
  - Keeps your ratings cache + taste model intact  
  - Rebuilds recommendations instantly  

---

## 8️⃣ Cache System Explained

The server persists data into:

```
cache/
tmdb_cache.json # TMDb metadata for every film you’ve touched
derived_cache.json # taste model + recommendations + rewatch ranking
hidden_rewatches.json # rewatches you chose to hide in the UI
data_state.json # current ratings + watchlist + overlap (so restarts are instant)

exported/
ranked-watchlist-*.csv # Letterboxd-ready ranked list exports
```

### What this means:
- First run → slow (lots of TMDb calls)
- Every later run → instant  
- Rankings, genre profile, rewatches, everything loads from disk

### When to delete cache:
- You updated your Letterboxd CSVs  
- You changed the taste model algorithm  
- You want a clean rebuild
- If you want to fully factory reset back to the raw CSVs without deleting files manually, you can also use the `Reset state to CSV` button in the UI (which calls `/api/reset-state`).

Delete with:

**macOS / Linux**
```
rm -rf cache
```

**Windows PowerShell**
```
Remove-Item cache -Recurse -Force
```

Then restart:
```
node server.js
```

---

## 🔍 Taste Model Algorithm (Final Summary)

Taste Matcher builds a **multi-dimensional taste vector** from your Letterboxd ratings, and applies it to every film in your watchlist.

### 1. Data Inputs
From `ratings.csv` and `watchlist.csv`:
- Title, year
- Your rating (0–5)
- Letterboxd URL
- Rewatch detection via URL matching

### 2. TMDb Metadata (enriched & cached)
For every film (ratings + watchlist):
- Genres
- Directors / Writers
- Production countries → Regions (Europe, Asia, NA, etc.)
- Release year → Decade (1980s, 2010s…)
- Keywords (themes, tone, mood)
- Collection ID & name (sagas, franchises)
- vote_average (community baseline)

### 3. Taste Profiles (averages)
From all your rated films, the backend builds:

```
genreProfile[genreId]
directorProfile[name]
writerProfile[name]
countryProfile["US"/"JP"/etc]
regionProfile["Asia"/"Europe"]
decadeProfile[1970, 1980...]
keywordProfile["surrealism", "slow-burn"...]
collectionProfile[id]
```

Each value = **average rating you give that factor** (Bayesian-smoothed later).

### 4. Film-Specific Affinity (0–1)
For each watchlist film, we compute:

- genreAffinity  
- directorAffinity  
- writerAffinity  
- keywordAffinity  
- countryRegionAffinity  
- decadeAffinity  
- collectionAffinity  
- tmdbScoreNorm  
- neighbourSimilarity – based on the top similar films in your rated history (using genres, keywords, decade, regions, directors)


### 5. Final Weighted Score
```
predictedScore =
 0.18 * genreAffinity +
  0.10 * directorAffinity +
  0.08 * writerAffinity +
  0.22 * keywordAffinity +
  0.07 * countryRegionAffinity +
  0.05 * decadeAffinity +
  0.05 * collectionAffinity +
  0.15 * tmdbScoreNorm +
  0.10 * neighbourSimilarity
```
*(genreAffinity, directorAffinity, etc. are 0–1; neighbourSimilarity is the k-NN component based on films you’ve already rated.)*

### 6. Ranking
- Converted to percent match  
- Sorted descending  
- Rendered as your ranked watchlist  

Rewatches receive a separate ranking:
```
rewatchScore = 0.6*(userRatingNorm) + 0.4*(predictedScore)
```


## 9️⃣ Quickstart Summary

clone repo
add ratings.csv & watchlist.csv to /data
add TMDB_API_KEY to .env
npm install
node server.js
open http://localhost:3000/
done. enjoy your personalized watchlist ranking ✨

---

## 📜 License

MIT License – see [LICENSE](./LICENSE) for full details.

