# JD Radar

JD Radar is an autonomous agent built on Claude Code that scans current
Indian **AI Product Manager** and **Product Manager** job postings, extracts
the skills employers are demanding right now, ranks them by frequency, and
produces a clean market intelligence report.

## How it works

1. **`jd-scrape-agent`** (powered by the `jd-scraper` skill) searches Google
   Jobs, company websites, greenhouse, Naukri, and Indeed, all in India for relevant postings in MNCs like google, facebook, servicenow, etc or deloitte or similar consulting firms or industry companies and collects raw,
   structured JD data — title, company, location, source, URL, and JD text.
2. **`jd-analyzer`** skill normalizes and counts skill mentions across the
   collected postings and ranks them by how many distinct JDs mention each
   one.
3. A ranked **market intelligence report** is saved to
   `outputs/skill-report-YYYY-MM-DD.md` and summarized in chat.

## Usage

From within this project folder, run:

```
/run-radar
```

This triggers the full pipeline: scrape ~20 job postings or more, analyze and rank
the skills mentioned, and write a dated report to `outputs/`.

## Project structure

```
JD-Radar/
├── CLAUDE.md                       # project rules: objective, sources, output format, constraints
├── README.md
├── .claude/
│   ├── skills/
│   │   ├── jd-scraper/SKILL.md     # how to search and extract JD data
│   │   └── jd-analyzer/SKILL.md    # how to normalize, count, and rank skills
│   ├── agents/
│   │   └── jd-scrape-agent.md      # worker that performs the scraping
│   └── commands/
│       └── run-radar.md            # orchestrates the full pipeline
└── outputs/                         # dated skill-frequency reports land here
```
