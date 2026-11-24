# Leakage Audit — Steam 2025 (`df_games`, 46 columns)

## A. Safe Pre-Launch (32 columns)
- **IDs & split keys** (for joins/splits only, don't feed raw IDs to models): `appid`, `release_date`, `release_year`
- **Store page info:** `name`, `type` (already filtered to `game`), `is_free`, `required_age`, `short_description`, `supported_languages`, `header_image`, `background` (use presence/length flags, not raw URLs; note `name`, `is_free`, and `mat_supports_*` can occasionally shift post-launch via rebrands, F2P conversions, or macOS/Linux ports)
- **OS & hardware specs:** `mat_supports_windows`, `mat_supports_mac`, `mat_supports_linux`, `mat_pc_os_min`, `mat_pc_processor_min`, `mat_pc_memory_min`, `mat_pc_graphics_min`, `mat_pc_os_rec`, `mat_pc_processor_rec`, `mat_pc_memory_rec`, `mat_pc_graphics_rec`
- **Joined metadata:** `n_cats`, `cats`, `n_devs`, `devs`, `n_genres`, `genres`, `n_platforms`, `platforms`, `n_pubs`, `pubs`

## B. Ambiguous / Snapshot Caveats (3 columns)
- `mat_initial_price`, `mat_currency` — captured at scrape time, not guaranteed day-one launch price. Safe as baseline price features once currencies are handled.
- `mat_achievement_count` — usually set at launch, though some devs patch achievements in later. Safe with caveat.

## C. Post-Launch Leakage — Drop All (11 columns)
- `metacritic_score`, `recommendations_total`
- `mat_final_price`, `mat_discount_percent`
- `eda_review_cnt`, `eda_pos_count`, `eda_pos_rate`, `eda_first_review_ts`, `eda_last_review_ts`
- `created_at`, `updated_at` (scraper timestamps, not game traits)

## Why 5 Leakage Columns Fail at Launch
1. **`eda_review_cnt` / `recommendations_total`:** Counts post-launch buyers. At release ($t = 0$), every game has 0 reviews, so a model relying on this breaks completely in production.
2. **`eda_pos_rate` / `eda_pos_count`:** Summarizes player sentiment after buying and playing. Doesn't exist before launch and is `NaN` for unreviewed games.
3. **`eda_first_review_ts` / `eda_last_review_ts`:** Timestamps of real user reviews. Impossible to have before release, and `last_review_ts` leaks how long the game stayed commercially active.
4. **`metacritic_score`:** Critic reviews publish at or after release (plus 98% of rows are empty anyway).
5. **`mat_discount_percent` / `mat_final_price`:** Shows whether a game happened to be on sale when scraped — post-launch discounting, not a pre-launch attribute.