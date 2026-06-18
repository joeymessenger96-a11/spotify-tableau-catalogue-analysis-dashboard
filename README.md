# Spotify Music Catalogue Analysis — Tableau Dashboard

An interactive two-page Tableau Public dashboard analysing a catalogue of **8,577 Spotify tracks** across **2,545 artists** — exploring artist reach, release growth, and the genre and album-type composition of the library.

**▶️ [View the live interactive dashboard](PASTE_YOUR_TABLEAU_PUBLIC_URL_HERE)**

---

## Overview

This project takes a raw, messy Spotify catalogue export and turns it into a navigable analytical product. It's built as two linked dashboard pages with reciprocal navigation, parameter-driven interactivity, and insight-led framing throughout — designed so a reader can both *explore* the data and immediately *read* the story.

**Tools:** Tableau Public · calculated fields · parameters · Level-of-Detail (LOD) expressions

---

## The two pages

### Page 1 — Overview
- **KPI banner:** Total Tracks (8,577), Avg Popularity (52.35), Unique Artists (2,545)
- **Top Artists** — a follower-count leaderboard (Taylor Swift leads at ~145M), with a dynamic Top-N slider
- **Releases Over Time** — a line chart surfacing the central finding: an exponential rise in releases after 2010, the streaming era's signature

### Page 2 — Genres & Albums
- **Genre Breakdown** — top-15 genres by share of catalogue; no single genre exceeds ~4%, revealing a highly fragmented mix
- **Album Type** — albums dominate over singles and compilations

The two pages are connected by **navigation buttons** in both directions, so the dashboard behaves like a small product rather than a stack of charts.

---

## Interactive features

| Feature | What it does | How it's built |
|---|---|---|
| **Metric swap** | Switch the artist metric (followers / popularity) on the fly | `Select Metric` string parameter → `Selected Metric` CASE calculated field |
| **Dynamic Top-N** | Adjust how many top artists are shown (5–30) | `Top N Artists` integer parameter wired into a rank filter |
| **% of Total Tracks** | Each genre as a share of the whole catalogue | FIXED LOD: `COUNT([Track Id]) / SUM({FIXED : COUNT([Track Id])})` |
| **Page navigation** | Move between Overview and Genres & Albums | Reciprocal Tableau navigation buttons |

---

## Analytical decisions worth noting

These are the judgement calls behind the dashboard — the *why*, not just the *what*.

**Cleaning the genre field.** The raw `artist_genres` column held **661 distinct values** — far too granular to chart meaningfully, with a long tail of one-off labels and N/A entries. I filtered to the **top 15 genres by count** and excluded N/A. This keeps the chart legible while preserving the genres that actually shape the catalogue, and it's a deliberate, defensible cleaning choice rather than an arbitrary cut.

**Followers over popularity for the artist leaderboard.** Spotify's popularity score is a **capped 0–100 index**, so among top artists it compresses — every megastar sits near 90–100 and the bars barely differ. Switching the leaderboard to **follower count** (uncapped) restores real differentiation and a clear ranking. The metric-swap parameter still lets a viewer toggle back to popularity to see that compression for themselves.

**Decoupling genre share from the artist metric.** The metric-swap parameter was originally wired into the genre chart too, which meant toggling the artist metric distorted the genre bars. A *breakdown* of catalogue composition shouldn't move when you change an unrelated artist metric — so I bound the genre chart to **% of Total Tracks** instead, making it stable and self-consistent (bar length and labels finally agree).

---

## Key findings

1. **Releases exploded after 2010** — the catalogue is near-flat from the 1950s to 2000, then rises exponentially, mirroring the streaming era's impact on release volume.
2. **The catalogue is genre-diverse** — no single genre accounts for more than ~4% of tracks; soundtrack and pop lead a long, fragmented tail.
3. **Albums dominate** the catalogue over singles and compilations.
4. **Follower reach is highly concentrated** at the top — a handful of artists command followings an order of magnitude above the rest.

---

## Repository contents

- `README.md` — this case study
- `images/` — dashboard screenshots
- `spotify-catalogue-analysis.twbx` — *(optional)* packaged Tableau workbook, if the source data is freely redistributable. Open in Tableau Desktop / Public to inspect the calculated fields, parameters, and LOD logic.

> **Note on the workbook:** a `.twbx` bundles the source data. Include it only if the dataset's licence permits redistribution; otherwise a data-free `.twb` or the live link alone is the safer choice.

---

## Data

- **Source:** Spotify catalogue export (`spotify_data clean.csv`)
- **Grain:** one row per track
- **Key columns:** `track_id`, `track_name`, `track_popularity`, `artist_name`, `artist_popularity`, `artist_followers`, `artist_genres`, `album_name`, `album_release_date`, `album_type`, `track_duration_min`
- **Derived fields:** Release Year, Selected Metric, % of Total Tracks
