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

Your entire watchlist is ranked using a **multi-factor taste model**, not just genres.

For each film on your watchlist, the backend considers:

- **Genre affinity** – how much you tend to like its genres  
- **Director affinity** – how highly you rate that director’s previous work  
- **Writer affinity** – how highly you rate that writer/screenwriter’s work  
- **Keyword / tone affinity** – how much you like its themes/moods (from TMDb keywords)  
- **Country & region affinity** – whether it comes from film cultures you rate highly  
- **Decade affinity** – how much you like films from that era (70s, 90s, 2010s, etc.)  
- **Collection affinity** – if it’s part of a collection/franchise you’ve rated before  
- **TMDb community rating** – global baseline quality signal  

These factors are normalized to a 0–1 scale and blended into a single **predictedScore**.
The UI still shows:

- Poster  
- Title  
- Genres  
- TMDb rating  
- **User genre score** (your genre-based taste for this film)  
- **Model score** (multifactor score)  
- **Match %** (with colored progress bar)  
- Rank number in your watchlist  
---

## 7️⃣ API Endpoints

| Route | Description |
|-------|-------------|
| `/api/ratings` | Ratings data (raw) |
| `/api/watchlist` | Watchlist after removing rewatches |
| `/api/overlap` | Detailed rewatch list |
| `/api/genre-profile` | Full taste model |
| `/api/genre-titles/:genreId` | All rated films in a specific genre |
| `/api/recommendations` | Ranked watchlist |

---

## 8️⃣ Cache System Explained

The server auto-creates:

cache/
tmdb_cache.json
derived_cache.json


Delete the cache **anytime you update the CSV files**:

### macOS / Linux
rm -rf cache


### Windows PowerShell
Remove-Item cache -Recurse -Force


Then: 
node server.js


---

## 🔍 Taste Model Algorithm (Final Summary)

The recommender is no longer “just genres”. It builds a **multi-dimensional taste profile** from your ratings and applies it to each film in your watchlist.

### 1. Input data

From **ratings.csv**:

- Title, year
- Your rating (0–5)
- Letterboxd URL

From **watchlist.csv**:

- Title, year
- Letterboxd URL

### 2. Rewatch detection

- Match films by **Letterboxd URL**
- If a film appears in **both** ratings & watchlist:
  - Treat it as a **rewatch**
  - Use its rating to learn your taste
  - Remove it from the watchlist ranking

### 3. TMDb metadata enrichment

For every rated and watchlist title, the backend fetches from TMDb:

- **Genres**
- **Production countries** → mapped to **regions** (Europe, Asia, etc.)
- **Release year** → mapped to **decade** (1970s, 2010s, etc.)
- **Belongs-to-collection** (franchises / sagas)
- **Credits (crew)**:
  - Directors
  - Writers / screenwriters
- **Keywords** (mood/tone/themes)
- **vote_average** (TMDb rating)

All of this is cached to disk so it only happens once.

### 4. Building the user taste profiles

From your rated films, the system computes average ratings for:

- **Genres** → `genreProfile[genreId] = avg rating`
- **Directors** → `directorProfile[name] = avg rating`
- **Writers** → `writerProfile[name] = avg rating`
- **Countries** → `countryProfile[countryCode] = avg rating`
- **Regions** → `regionProfile[regionName] = avg rating`
- **Decades** → `decadeProfile[1970, 1980, ...] = avg rating`
- **Keywords** → `keywordProfile[keyword] = avg rating`
- **Collections** → `collectionProfile[collectionId] = avg rating`

This gives you a **taste vector** across multiple axes: who made it, where it’s from, when it’s from, what it feels like, and what “universe” it belongs to.

### 5. Scoring each watchlist film

For each film on your watchlist, the model computes **affinity scores** (0–1) for:

- `genreAffinity` – how much you like its genres  
- `directorAffinity` – how much you like its director(s)  
- `writerAffinity` – how much you like its writer(s)  
- `keywordAffinity` – how much you like its tone/themes (keywords)  
- `countryRegionAffinity` – how much you like cinema from its country/region  
- `decadeAffinity` – how much you like films from its decade  
- `collectionAffinity` – how much you like its collection/franchise  
- `tmdbScoreNorm` – TMDb’s vote_average normalized to 0–1  

Internally, these start as average ratings on a 0–5 scale and are normalized to 0–1.

### 6. Final prediction formula

All affinities are combined into one **predictedScore**:

> `predictedScore =`  
> `  0.15 * genreAffinity`  
> `+ 0.20 * directorAffinity`  
> `+ 0.10 * writerAffinity`  
> `+ 0.20 * keywordAffinity`  
> `+ 0.10 * countryRegionAffinity`  
> `+ 0.05 * decadeAffinity`  
> `+ 0.05 * collectionAffinity`  
> `+ 0.15 * tmdbScoreNorm`

This weight choice emphasizes:

- **who made it** (director/writer)  
- **what it feels like** (keywords / tone)  
- **what kind of film culture it comes from** (country/region/decade)  
- **your overall genre preferences**  
- **global consensus** (TMDb rating)

### 7. Ranking & UI

- `predictedScore` is used to rank the entire watchlist  
- Scores are converted into a **percentage match** for the UI  
- The API still exposes `userGenreScore` along with the new breakdown, so you can debug or explain the model easily in a viva


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

