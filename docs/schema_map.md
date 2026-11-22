\# Steam 2025 — Schema Map



\## Core Tables

\- `applications.csv` — 239,664 rows. 1 row per app (`appid` is unique).

\- `reviews.csv` — 1,048,148 rows. Many rows per game (`recommendationid` is unique, `appid` repeats).



\## Lookup Tables (`id` -> `name`)

All have unique `id`:

\- `categories.csv` (462 rows)

\- `developers.csv` (101,226 rows)

\- `genres.csv` (154 rows)

\- `platforms.csv` (3 rows)

\- `publishers.csv` (85,699 rows)



\## Bridge Tables (`appid` <-> foreign id)

1:many with `applications.csv` (`appid` repeats in all of them):

\- `application\_categories.csv` (1,077,156 rows)

\- `application\_developers.csv` (250,817 rows)

\- `application\_genres.csv` (587,515 rows)

\- `application\_platforms.csv` (334,671 rows)

\- `application\_publishers.csv` (223,048 rows)



\## Notes for Day 2

\- \*\*Don't join bridge tables raw.\*\* Tested `applications` + `application\_genres` — rows jumped from 239,664 to 619,237 because games have multiple genres. Group/aggregate to 1 row per `appid` before merging.

\- \*\*Filter junk early.\*\* `applications.csv` mixes actual games with DLCs, demos, and soundtracks (`type`), and 15% of rows are missing `release\_date`.

\- \*\*Skip dead columns.\*\* `metacritic\_score` (98% missing) and `recommendations\_total` (91% missing) are basically empty. `mat\_initial\_price` is 40% missing (check `is\_free` first).

