---
name: jd-scraper
description: Use when searching for and extracting structured data from Indian AI Product Manager / Product Manager job postings at large MNCs, consulting firms, and tech companies via Google Jobs, LinkedIn, Greenhouse, company career sites, Naukri, and Indeed India. Teaches search query patterns, what fields to extract, and how to handle blocked or JS-heavy pages.
---

# JD Scraper Skill

This skill teaches how to find and extract job description (JD) data for
Indian AI Product Manager (AI PM) and Product Manager (PM) roles. It does not
analyze or rank anything — its job is to produce clean, raw, structured JD
records for the `jd-analyzer` skill to process.

## Target roles & queries

Search for postings matching roles like:
- "AI Product Manager"
- "Product Manager AI" / "Product Manager"
- "Senior Product Manager"
- "Product Owner"
- "Product Manager" — general, India-based

## Companies to search for

Focus on large MNCs and well-known employers with active India PM hiring:
- **Big tech / software:** Google, Amazon, Meta/Facebook, Microsoft,
  Salesforce, Adobe, ServiceNow, SAP, Oracle, Atlassian
- **Consulting (Big 3 / Big 4 and similar):** Deloitte, PwC, EY, KPMG,
  McKinsey, BCG, Bain, Accenture
- **Other large industry firms:** Mercedes-Benz, BMW, and similar global
  enterprises with India product/engineering hubs

Combine company name + role + location in queries, e.g.:
- `Product Manager AI Google India jobs`
- `Product Manager Amazon India site:linkedin.com/jobs`
- `Deloitte Product Manager India site:linkedin.com/jobs`
- `ServiceNow Product Manager India site:boards.greenhouse.io`
- `BMW Product Manager India careers`

## Sites to search

Use WebSearch with queries that combine role + company/location + site, e.g.:
- `AI Product Manager jobs India site:naukri.com`
- `Product Manager AI India site:in.indeed.com`
- `AI Product Manager India jobs`
- `Product Manager India hiring AI site:naukri.com`
- `Product Manager India site:linkedin.com/jobs`
- `Product Manager India site:boards.greenhouse.io`
- `Product Manager India site:<company-domain>/careers` (company-owned
  career pages, e.g. `careers.google.com`, `amazon.jobs`)

Sources to draw from:
- Google Jobs (via web search, India-focused queries)
- LinkedIn (linkedin.com/jobs)
- Greenhouse (boards.greenhouse.io / job-boards.greenhouse.io)
- Company-owned career pages
- Naukri.com
- Indeed India (in.indeed.com)

Vary phrasing, companies, and locations across searches to avoid duplicate
results and to reach the 20-25 JD target.

## Extraction method

1. **Primary: WebSearch.** Most search results return enough snippet text to
   identify title, company, location, and a partial JD excerpt — often
   including key skills/tools mentioned. Capture this directly; it is the
   main data source.
2. **Fallback: WebFetch.** For promising URLs (company career pages,
   Greenhouse postings, and Indeed India listings are sometimes fetchable),
   attempt WebFetch to retrieve the full JD text. If the page is blocked,
   returns a CAPTCHA, login wall, or empty/JS-only content (common on
   LinkedIn and Naukri), discard the fetch attempt and rely on the WebSearch
   snippet instead — do not retry repeatedly on the same blocked domain.

## Fields to extract per posting

For each JD, record:
- `title` — job title as posted
- `company` — employer name (if available)
- `location` — city/region (if available)
- `source` — naukri / indeed / google_jobs / linkedin / greenhouse /
  company_website
- `url` — link to the original posting
- `jd_text` — the JD text/snippet retrieved (skills, responsibilities,
  requirements — as much as available)

## Output

Return the collected postings as a structured list (JSON or markdown table)
to be handed off to the `jd-analyzer` skill. Do not deduplicate aggressively —
similar postings from different sources are fine, but skip exact duplicate
URLs.

## Notes

- Stop once ~20-25 usable postings are collected, or after a reasonable
  number of search queries (diminishing returns).
- If a source consistently returns nothing usable (e.g., heavily blocked),
  note this in the output and move on rather than wasting more queries on it.
