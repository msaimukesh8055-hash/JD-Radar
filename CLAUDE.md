# JD Radar

## Objective
JD Radar is an autonomous market-intelligence agent that scans current Indian
AI Product Manager (AI PM) and Product Manager (PM) job postings, extracts the
skills employers are demanding right now, ranks them by frequency, and
produces a clean market intelligence report.

## Sources
- Google Jobs (via web search, India-focused queries)
- Naukri.com
- Indeed India (in.indeed.com)

Target: 40-50 job descriptions per run, focused on roles such as:
- "AI Product Manager"
- "Product Manager - AI" / "Product Manager (AI/ML)"
- "Product Manager" (India, general)

## Pipeline
1. `/run-radar` is the entry point.
2. It invokes `jd-scrape-agent` (using the `jd-scraper` skill) to collect raw,
   structured JD data — title, company, location, source, URL, and JD
   text/snippet. The agent does NOT analyze or rank anything.
3. The `jd-analyzer` skill is applied to the raw JD data to normalize, count,
   and rank the skills/keywords mentioned across postings.
4. A final report is written to `outputs/skill-report-YYYY-MM-DD.md` and
   summarized in chat.

## Output Format
The report must include:
- Run metadata (date, number of JDs analyzed, sources used, queries run)
- Top skills table, ranked by frequency (skill, count, % of JDs mentioning it)
- Notable emerging/niche skills (mentioned in fewer JDs but worth flagging)
- Brief methodology note (sources, search queries used, limitations)

## Constraints
- WebSearch is the primary data collection method (resilient to anti-bot
  blocks, returns snippet-level detail). WebFetch may be used as a
  best-effort fallback for individual JD pages on sites that allow it.
- Do not fabricate job postings or skill data — only report on what was
  actually retrieved during the run.
- Respect robots.txt / site terms; do not attempt to bypass CAPTCHAs, logins,
  or anti-bot protections.
- Keep reports concise and skimmable — this is a portfolio deliverable.
