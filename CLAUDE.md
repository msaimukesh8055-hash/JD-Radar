# JD Radar

## Objective
JD Radar is an autonomous market-intelligence agent that scans current Indian
AI Product Manager (AI PM) and Product Manager (PM) job postings, extracts the
skills employers are demanding right now, ranks them by frequency, and
produces a clean market intelligence report.

## Sources
- Google Jobs (via web search, India-focused queries)
- company websites or pages
- Greenshouse
- Naukri.com
- Indeed India (in.indeed.com)
- Linkedin

Target: 20-25 job descriptions per run, focused on roles such as:
- "Product Manager"
- "AI Product Manager"
- "Product Owner"
- "Product Manager - AI" / "Product Manager"
- "Product Manager" (India, general)

## Companies to search for: search only in MNC companies like 
- Google, Facebook, Amazon, etc
- Consulting firms like Big4 and Big3 firms
- Any other industry companies 

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
- Run metadata (date, total_JDs_analyzed — valid deduplicated JDs used,
  sources used, companies covered)
- Top skills summary: skill name and the number of JDs that mention or
  signal it, ranked by count, top 15-20. No percentages, categories, or
  CRITICAL flags — just skill + count.
- Company-by-company breakdown: for every JD analyzed, list the company,
  role title, location, and the top 3-6 skills that JD signals.
- Key takeaways: analysis and synthesis of the findings, including the
  analyst's own observations on patterns and what they mean.
- Brief methodology note (sources, search queries used, JD dedup/exclusion
  count, limitations)

## Constraints
- WebSearch is the primary data collection method (resilient to anti-bot
  blocks, returns snippet-level detail). WebFetch may be used as a
  best-effort fallback for individual JD pages on sites that allow it.
- Do not fabricate job postings or skill data — only report on what was
  actually retrieved during the run.
- Respect robots.txt / site terms; do not attempt to bypass CAPTCHAs, logins,
  or anti-bot protections.
- Keep reports concise and skimmable — this is a portfolio deliverable.
