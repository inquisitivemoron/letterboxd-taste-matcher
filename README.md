# 🎬 Taste Matcher – Letterboxd + TMDb Watchlist Ranker

A backend-first Node.js project that takes:

- your **Letterboxd ratings export**, and  
- your **Letterboxd watchlist export**

…then:

1. Identifies and removes **rewatches** (movies appearing in both your ratings & watchlist).  
2. Uses your **ratings + TMDb genres** to compute a personalized **taste profile**.  
3. Uses that profile to **rank your entire watchlist** based on how well each film matches your taste.  
4. Serves a clean, simple **local web UI**:
   - Rewatch list (with posters + your rating)  
   - Genre profile (clickable genre cards → see all films you've rated in that genre)  
   - Ranked watchlist (full posters, match %, TMDb data)

Everything runs **locally** — no external frontend frameworks or databases required.

---

## 🧱 Tech Stack

- **Node.js + Express**
- **TMDb API** (for movie/TV metadata)
- **csv-parse** (parsing Letterboxd CSV files)
- **HTML + CSS + vanilla JS** (simple frontend)
- **Disk-based JSON caching** (fast restarts, no repeated API calls)

---

## 📁 Project Structure
taste-matcher/
├─ data/
│ ├─ ratings.csv # provided by you
│ └─ watchlist.csv # provided by you
├─ public/
│ └─ index.html # user interface
├─ cache/
│ ├─ tmdb_cache.json # auto-generated TMDb details cache
│ └─ derived_cache.json # auto-generated taste model + recs
├─ server.js # main backend
├─ .env # user adds TMDb API key here
└─ package.json

> ⚠️ Do NOT commit your `.env`, CSV files, or `/cache/` to a public GitHub repo.

---

## 1️⃣ Prerequisites

- Node.js **v18+**
- A **TMDb account** + API key
- Your **Letterboxd ratings and watchlist CSV files**

---

## 2️⃣ Preparing Your Letterboxd CSV Files

Both files go into:


taste-matcher/data/


### 2.1 Ratings Export

1. Go to **Letterboxd → Settings → Data → Export**  
2. Download your **Ratings** CSV  
3. Rename it to:

ratings.csv


### 2.2 Watchlist Export

1. Open your **Watchlist** on Letterboxd  
2. Click **Export**  
3. Rename it to:

watchlist.csv

---

## 3️⃣ Setting Up TMDb API Key

1. Go to: https://www.themoviedb.org/settings/api  
2. Request a **Developer API key**  
3. In this project, open the `.env` file (already included):

TMDB_API_KEY=YOUR_KEY_HERE
PORT=3000


Replace `YOUR_KEY_HERE` with your actual TMDb API key.

> ⚠️ Never publish your `.env` file on GitHub.

---

## 4️⃣ Install Dependencies

Inside the project directory:

```bash
npm install


5️⃣ Run the Server

What happens on first run:

Loads CSV files

Detects rewatches

Fetches metadata from TMDb

Builds genre profile

Builds watchlist ranking

Saves everything to /cache/

First run may take a few minutes, depending on your data size.

After that

Due to caching:

No TMDb calls

No recomputation

Startup is instant
6️⃣ Open the UI

Open your browser and visit:


