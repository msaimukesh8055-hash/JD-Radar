---
description: Run the full JD Radar pipeline - scrape Indian AI PM/PM job postings, rank in-demand skills, and save a market intelligence report.
---

Run the full JD Radar pipeline end to end:

1. **Scrape.** Invoke the `jd-scrape-agent` agent to collect ~20-25 raw,
   structured JD records (title, company, location, source, url, jd_text) for
   Indian AI Product Manager and Product Manager roles, per the `jd-scraper`
   skill from from:
   - FAANG/MNC tech companies (Google, Meta, Microsoft, Amazon, ServiceNow, or similar product companies)
   - Big 4 / Big 3 consulting firms (Deloitte, Bain, BCG, McKinsey, PwC, EY)
   - Other large product-focused MNCs in India

2. **Analyze.** Apply the `jd-analyzer` skill to the collected JD records:
   normalize skill mentions, count how many distinct JDs mention each skill,
   and rank them into a top-skills tier and an emerging/niche-skills tier.

3. **Report.** Write the final report to
   `outputs/skill-report-YYYY-MM-DD.md` (using today's date) following the
   output format defined in `CLAUDE.md`: run metadata, top skills table,
   emerging skills, and methodology notes.

4. **Summarize.** Present the top skills table and key takeaways directly in
   chat, and confirm the path of the saved report.

If any source is unreachable or blocked, note it in the methodology section
and proceed with whatever data was successfully collected.
