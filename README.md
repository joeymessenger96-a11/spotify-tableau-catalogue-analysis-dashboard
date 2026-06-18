# Spotify Music Catalogue Analysis (Tableau Dashboard)

An interactive two-page Tableau Public dashboard I built to analyse a catalogue of **8,577 Spotify tracks** across **2,545 artists**, looking at artist reach, release growth, and how the catalogue breaks down by genre and album type.

**[View the live interactive dashboard](https://public.tableau.com/views/SpotifyMusicCatalogueAnalysis/CatalogueOverview)**

![Overview page](images/Catalogue%20Overview.png)

## Overview

I took a raw, messy Spotify catalogue export and turned it into something you can actually navigate and read. It's two linked dashboard pages with navigation between them, parameter-driven interactivity, and titles that lead with the insight, so you can explore the data or just read the story off the page.

**Tools:** Tableau Public, calculated fields, parameters, Level-of-Detail (LOD) expressions

## The two pages

### Page 1: Overview
* **KPI banner:** Total Tracks (8,577), Avg Popularity (52.35), Unique Artists (2,545)
* **Top Artists:** a follower-count leaderboard (Taylor Swift leads at roughly 145M), with a dynamic Top-N slider
* **Releases Over Time:** a line chart that surfaces my main finding, an exponential rise in releases after 2010 that tracks the streaming era

### Page 2: Genres & Albums
* **Genre Breakdown:** top 15 genres by share of the catalogue. No single genre gets above about 4%, which says the mix is genuinely fragmented
* **Album Type:** albums dominate over singles and compilations

![Genres & Albums page](images/Genre%20&%20Albums%20Breakdown.png)

I connected the two pages with navigation buttons in both directions, so the whole thing behaves like a small product rather than a stack of charts.

## Interactive features

| Feature | What it does | How I built it |
|---|---|---|
| **Metric swap** | Switch the artist metric (followers or popularity) on the fly | `Select Metric` string parameter feeding a `Selected Metric` CASE calculated field |
| **Dynamic Top-N** | Adjust how many top artists show (5 to 30) | `Top N Artists` integer parameter wired into a rank filter |
| **% of Total Tracks** | Each genre as a share of the whole catalogue | FIXED LOD: `COUNT([Track Id]) / SUM({FIXED : COUNT([Track Id])})` |
| **Page navigation** | Move between Overview and Genres & Albums | Reciprocal Tableau navigation buttons |

## Decisions I made along the way

These are the judgement calls behind the dashboard, the why rather than just the what.

**Cleaning the genre field.** The raw `artist_genres` column had **661 distinct values**, way too granular to chart, with a long tail of one-off labels and N/A entries. I filtered to the **top 15 genres by count** and dropped N/A. That keeps the chart readable while keeping the genres that actually shape the catalogue. It's a deliberate cleaning choice, not an arbitrary cut, and I can defend it.

**Why I used followers instead of popularity for the artist leaderboard.** Spotify's popularity score is a **capped 0 to 100 index**, so among the top artists it compresses. Every megastar sits near 90 to 100 and the bars barely differ. Switching to **follower count**, which isn't capped, brings back real differentiation and a clear ranking. I kept the metric-swap parameter so anyone can toggle back to popularity and see that compression for themselves.

**Decoupling genre share from the artist metric.** I originally had the metric-swap parameter wired into the genre chart too, which meant toggling the artist metric distorted the genre bars. A breakdown of catalogue composition shouldn't move when you change an unrelated artist metric, so I bound the genre chart to **% of Total Tracks** instead. Now it's stable and the bar lengths actually match their labels.

## What I found

1. **Releases exploded after 2010.** The catalogue is near flat from the 1950s to 2000, then climbs exponentially, which mirrors what streaming did to release volume.
2. **The catalogue is genre-diverse.** No single genre is more than about 4% of tracks. Soundtrack and pop lead a long, fragmented tail.
3. **Albums dominate** over singles and compilations.
4. **Follower reach is concentrated at the top.** A handful of artists have followings an order of magnitude above everyone else.

## What's in this repo

* `README.md`: this write-up
* `images/`: dashboard screenshots
* `spotify-catalogue-analysis.twbx` (optional): the packaged Tableau workbook, if I include it. Open it in Tableau to see the calculated fields, parameters, and LOD logic.

> Note on the workbook: a `.twbx` bundles the source data inside it, so I'd only include it if the dataset's licence allows redistribution. Otherwise a data-free `.twb` or just the live link is the safer call.

## Data

* **Source:** Spotify catalogue export (`spotify_data clean.csv`)
* **Grain:** one row per track
* **Key columns:** `track_id`, `track_name`, `track_popularity`, `artist_name`, `artist_popularity`, `artist_followers`, `artist_genres`, `album_name`, `album_release_date`, `album_type`, `track_duration_min`
* **Fields I derived:** Release Year, Selected Metric, % of Total Tracks
