# 🎬 Taste Matcher – Letterboxd + TMDb Watchlist Ranker

Taste Matcher is a backend-first Node.js project that:

- Reads your **Letterboxd ratings CSV**
- Reads your **Letterboxd watchlist CSV**
- Removes **rewatches** (movies appearing in both files)
- Builds a personalized **genre taste profile** using TMDb metadata
- Predicts how much you'll like every film in your watchlist
- Ranks the entire watchlist from most-likely-to-enjoy → least

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

Your entire watchlist ranked based on: 
taste_score = avg( your_rating_per_genre )
tmdb_score = vote_average / 10
final_score = 0.7 * taste_score + 0.3 * tmdb_score


Shows:

- Poster  
- Title  
- Genres  
- TMDb rating  
- User genre score  
- Model score  
- **Match %** (with colored progress bar)  
- Rank number  

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

1. **Identify rewatches** (same Letterboxd URL)
2. **Use ratings to compute genre averages**
3. **Fetch watchlist metadata** from TMDb:
   - genres
   - rating
   - poster  
4. **Score each film:**
userGenreScore = weighted average of user's genre ratings
tmdbScore = vote_average / 10

predictedScore = 0.7 * userGenreScore + 0.3 * tmdbScore

5. **Normalize → convert to % → sort descending**

---

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

