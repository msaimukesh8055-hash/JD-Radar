---
name: jd-scraper
description: Use when searching for and extracting structured data from Indian AI Product Manager / Product Manager job postings on Google Jobs, Naukri, and Indeed India. Teaches search query patterns, what fields to extract, and how to handle blocked or JS-heavy pages.
---

# JD Scraper Skill

This skill teaches how to find and extract job description (JD) data for
Indian AI Product Manager (AI PM) and Product Manager (PM) roles. It does not
analyze or rank anything — its job is to produce clean, raw, structured JD
records for the `jd-analyzer` skill to process.

## Target roles & queries

Search for postings matching roles like:
- "AI Product Manager"
- "Product Manager AI" / "Product Manager (AI/ML)"
- "Senior Product Manager" with AI/ML focus
- "Product Manager" — general, India-based

Use WebSearch with queries that combine role + location + source, e.g.:
- `AI Product Manager jobs India site:naukri.com`
- `Product Manager AI India site:in.indeed.com`
- `AI Product Manager India jobs`
- `Product Manager India hiring AI ML site:naukri.com`

Vary phrasing and run multiple searches to avoid duplicate results and to
reach the 40-50 JD target.

## Extraction method

1. **Primary: WebSearch.** Most search results return enough snippet text to
   identify title, company, location, and a partial JD excerpt — often
   including key skills/tools mentioned. Capture this directly; it is the
   main data source.
2. **Fallback: WebFetch.** For promising URLs (especially Indeed India
   listings, which are sometimes fetchable), attempt WebFetch to retrieve the
   full JD text. If the page is blocked, returns a CAPTCHA, login wall, or
   empty/JS-only content, discard the fetch attempt and rely on the WebSearch
   snippet instead — do not retry repeatedly on the same blocked domain.

## Fields to extract per posting

For each JD, record:
- `title` — job title as posted
- `company` — employer name (if available)
- `location` — city/region (if available)
- `source` — naukri / indeed / google_jobs
- `url` — link to the original posting
- `jd_text` — the JD text/snippet retrieved (skills, responsibilities,
  requirements — as much as available)

## Output

Return the collected postings as a structured list (JSON or markdown table)
to be handed off to the `jd-analyzer` skill. Do not deduplicate aggressively —
similar postings from different sources are fine, but skip exact duplicate
URLs.

## Notes

- Stop once ~40-50 usable postings are collected, or after a reasonable
  number of search queries (diminishing returns).
- If a source consistently returns nothing usable (e.g., heavily blocked),
  note this in the output and move on rather than wasting more queries on it.
