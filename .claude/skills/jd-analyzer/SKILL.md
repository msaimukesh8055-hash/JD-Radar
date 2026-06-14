---
name: jd-analyzer
description: Use when normalizing, counting, and ranking skills or keywords extracted from a batch of job descriptions, to produce a market-intelligence skill frequency report.
---

# JD Analyzer Skill

This skill teaches how to turn raw JD records (produced by the `jd-scraper`
skill) into a ranked skill-frequency report.

## Steps

1. **Normalize skill mentions.**
   - Merge synonyms/variants into a canonical form, e.g.:
     - "SQL", "Sql" → "SQL"
     - "A/B testing", "AB testing" → "A/B Testing"
     - "Gen AI", "GenAI", "Generative AI" → "Generative AI"
     - "LLM", "LLMs", "Large Language Models" → "LLMs"
     - "Stakeholder mgmt", "stakeholder management" → "Stakeholder Management"
   - Keep both hard skills (tools, technical concepts) and soft/domain skills
     (stakeholder management, go-to-market strategy, etc.) — both are useful
     market signal.

2. **Count frequency.**
   - For each canonical skill, count the number of distinct JDs (by URL) that
     mention it — not total mentions within a single JD.
   - Compute `% of JDs mentioning it = count / total_JDs_analyzed * 100`.

3. **Rank.**
   - Sort skills by count, descending.
   - Split into two tiers:
     - **Top skills** — the top 15-20 skills by count.
     - **Emerging/niche skills** — mentioned in only a few JDs but
       noteworthy (e.g., specific AI tools, frameworks, certifications).

4. **Write the report** following the output format defined in the project
   `CLAUDE.md`: run metadata, top skills table, emerging skills list,
   methodology notes.

## Output location

Save the final report to `outputs/skill-report-YYYY-MM-DD.md` (using the
current date), and also present the top skills table directly in chat.
