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

taste-matcher/
├─ data/
│ ├─ ratings.csv # your ratings (Letterboxd export)
│ └─ watchlist.csv # your watchlist (Letterboxd export)
├─ public/
│ └─ index.html # minimal UI
├─ cache/
│ ├─ tmdb_cache.json # TMDb responses saved here
│ └─ derived_cache.json # taste model + recommendations cache
├─ server.js # backend logic
├─ .env # TMDb API key
└─ package.json


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

Place both files inside:

taste-matcher/data/

### 🔹 Export Ratings

1. Letterboxd → **Settings → Data → Export**
2. Download **Ratings CSV**
3. Rename to: ratings.csv


### 🔹 Export Watchlist

1. Open your **Watchlist**
2. Click **Export**
3. Rename to: watchlist.csv


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
npm install


This will install everything listed inside `package.json`, including:

- express  
- axios  
- csv-parse  
- dotenv  

If this completes without errors, you're good.

---


### 4.2 If `npm install` fails or package.json is missing

Run the following manually:

npm init -y
npm install express axios csv-parse dotenv


This recreates a valid `package.json` and installs the required packages.

---

### 4.3 Verify installation

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
- **TMDb community rating** – normalized quality baseline  

These are normalized (0–1), weighted, and turned into a **predictedScore** for every watchlist film.

### UI includes:
- Poster  
- Title + year  
- Genres  
- TMDb rating  
- User-genre score  
- Multifactor model score  
- Match percentage (color-coded bar)  
- Rank number  

## 7️⃣ API Endpoints (Full CRUD)

| Route | Method | Description |
|-------|--------|-------------|
| `/api/ratings` | GET | Raw ratings list |
| `/api/watchlist` | GET | Watchlist with rewatches removed |
| `/api/overlap` | GET | Detailed rewatch list |
| `/api/genre-profile` | GET | Full multi-dimensional taste model |
| `/api/genre-titles/:id` | GET | Rated films filtered by a genre |
| `/api/recommendations` | GET | Ranked watchlist |
| `/api/rewatch-ranking` | GET | Ranked rewatches |
| `/api/mark-watched` | POST | Remove from watchlist → add/update rating |
| `/api/add-rating` | POST | Add a manual rating (with duplicate detection) |
| `/api/add-to-watchlist` | POST | Add to watchlist (rewatch detection built-in) |

---

## 8️⃣ Cache System Explained

The server persists data into:

```
cache/
  tmdb_cache.json       # TMDb metadata for every film you’ve touched
  derived_cache.json    # taste model + recommendations + rewatch ranking
```

### What this means:
- First run → slow (lots of TMDb calls)
- Every later run → instant  
- Rankings, genre profile, rewatches, everything loads from disk

### When to delete cache:
- You updated your Letterboxd CSVs  
- You changed the taste model algorithm  
- You want a clean rebuild  

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

### 5. Final Weighted Score
```
predictedScore =
  0.15 * genreAffinity +
  0.20 * directorAffinity +
  0.10 * writerAffinity +
  0.20 * keywordAffinity +
  0.10 * countryRegionAffinity +
  0.05 * decadeAffinity +
  0.05 * collectionAffinity +
  0.15 * tmdbScoreNorm
```

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

MIT License — free for personal & educational use.

