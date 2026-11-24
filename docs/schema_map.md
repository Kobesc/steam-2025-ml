# Steam 2025 — Schema Map

12 CSV tables + `MANIFEST.json` (13 files total in `data/raw/`).

## Core Tables
- `applications.csv` — 239,664 rows. 1 row per app (`appid` is unique).
- `reviews.csv` — 1,048,148 rows. Many rows per game (`recommendationid` is unique, `appid` repeats, capped at 100 reviews per app).

## Lookup Tables (`id` -> `name`)
All have unique `id`:
- `categories.csv` (462 rows)
- `developers.csv` (101,226 rows)
- `genres.csv` (154 rows)
- `platforms.csv` (3 rows)
- `publishers.csv` (85,699 rows)

## Bridge Tables (`appid` <-> foreign id)
`M:N` junction tables connecting `applications.csv` to lookup tables (`appid` repeats in all of them):
- `application_categories.csv` (1,077,156 rows)
- `application_developers.csv` (250,817 rows)
- `application_genres.csv` (587,515 rows)
- `application_platforms.csv` (334,671 rows)
- `application_publishers.csv` (223,048 rows)

## Notes for Day 2
- **Don't join bridge tables raw.** Tested `applications` + `application_genres` — rows jumped from 239,664 to 619,237 because games have multiple genres. Group/aggregate to 1 row per `appid` before merging.
- **Filter junk early.** `applications.csv` mixes actual games with DLCs, demos, and soundtracks (`type`), and 15% of rows are missing `release_date`.
- **Skip dead columns.** `metacritic_score` (98% missing) and `recommendations_total` (91% missing) are mostly empty. `mat_initial_price` is 40% missing (check `is_free` first).