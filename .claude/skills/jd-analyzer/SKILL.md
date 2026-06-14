---
name: jd-analyzer
description: Use when reading raw JD records to infer demanded skills from sentence-level context, normalize and count them, and produce a ranked market-intelligence skill frequency report
---

# JD Analyzer Skill

This skill teaches how to turn raw JD records (produced by the jd-scraper skill) into a ranked skill-frequency report.

## Steps

### 1. Read and infer skills from JD text

For each JD, read through every sentence of the description — not just any "skills" or "requirements" section.

For each sentence, infer what skill(s) it signals — even if the skill is not named explicitly. Be generous but accurate: infer the skill a reasonable PM reading this sentence would recognize as the underlying competency, not a stretch interpretation.

Examples of inference:
- "Partner with engineering and design teams to define product requirements" → Stakeholder Management, PRD Writing
- "Define and track success metrics for AI features" → Evals, Metrics Definition
- "Build and iterate on prompts for LLM-based features" → Prompt Engineering
- "Work with data science to deploy ML models into production" → MLOps, Cross-functional Collaboration
- "Own the roadmap for our AI agent platform" → AI Agents, Roadmapping, Strategy
- "Retrieve relevant context from internal knowledge bases for LLM responses" → RAG, Context Engineering
- "Write SQL queries to analyze user behavior" → SQL, Data Analysis
- "Communicate progress to leadership and cross-functional stakeholders" → Communications, Stakeholder Management

Build a per-JD list of inferred skills before moving to normalization.

### 2. Normalize skill mentions

Merge synonyms/variants into a canonical form, e.g.:
- "SQL", "Sql" → "SQL"
- "A/B testing", "AB testing" → "A/B Testing"
- "Gen AI", "GenAI", "Generative AI" → "Generative AI"
- "LLM", "LLMs", "Large Language Models" → "LLMs"
- "Stakeholder mgmt", "stakeholder management" → "Stakeholder Management"
- "Prompt design", "prompting", "prompt engineering" → "Prompt Engineering"
- "RAG", "retrieval augmented generation" → "RAG"

Keep both hard skills (tools, technical concepts) and soft/domain skills (stakeholder management, go-to-market strategy, etc.) — both are useful market signal.

### 3. Count frequency

For each canonical skill, count the number of distinct JDs (by URL) that mention or signal it — not total mentions within a single JD.

### 4. Rank into a top skills summary

Sort skills by count, descending. Take the top 15-20 skills.

This summary is just **skill name + count of distinct JDs** — no
percentages, no categories, no CRITICAL flags.

### 5. Build the company-by-company breakdown

For every valid JD, produce one entry with:
- **Company**
- **Role** (job title as posted)
- **Location**
- **Top skills** — the 3-6 most representative skills inferred for that
  specific JD (a short, readable shortlist, not the full inferred list)

This is the core of the report — it lets a reader scan "what does each
employer actually want" at a glance.

### 6. Handle data quality

Deduplicate JDs by URL before counting — if the same JD appears twice, count it once.

If a JD is missing or has unreadable description text, exclude it from the denominator and from the company-by-company breakdown, but note the exclusion count (and which postings were excluded) in the report's methodology section.

Total_JDs_analyzed = count of valid, deduplicated JDs actually used — state this number explicitly in the report header.

## Output

Write the report following the output format defined in the project CLAUDE.md:
run metadata, top skills summary (skill + count only), company-by-company
breakdown (company, role, location, top skills), key takeaways/analysis, and
methodology notes.

## Output location

Save the final report to `outputs/skill-report-YYYY-MM-DD.md` (using the current date), and also present the top skills table directly in chat.
