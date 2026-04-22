---
slug: community-annotation
path: /home/b/p/all-my-tiny-projects/community-annotation
objective: Scrape and organize community-annotated anime episodes as raw data for downstream tooling
status: active
created: 2026-04-22
---

# community-annotation

## Objective

Python + JS scraping scripts that walk anime sites to collect episode metadata
and play-button actions. Output lives in `jsons/`, `anime_episodes/`, and
`findin_anime_jsons/`. Intended as a data source, not a product.

## Context

- Scraper logic: `scraper.js`, `anime_scraper.js`, `episodes_scraper.js`.
- Python drivers: `scrape_pages.py`, `anime_scrape_pages.py`, `get_episodes.py`, `play_episode.py`.
- Urls list: `urls.txt`. Spec: `scraper-instruction.md`.

## Ideas

- [c4f0b71e] Publish the cleaned episode catalog as a static JSON API on GitHub Pages (added: 2026-04-22)

## Milestones

- [9d3a8b52] One-command full refresh that rebuilds every JSON dataset from urls.txt (added: 2026-04-22)

## Features

- [5a2c9e14] Dedup across anime_episodes/ and findin_anime_jsons/ — same episode id must collapse to one record (added: 2026-04-22)

## Todos

- [e0b7f3c8] Write a short README describing which script produces which output file (added: 2026-04-22)

## Doing

## Done
