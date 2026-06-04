# AI Job Search Framework

A structured, AI-powered job search system built for [Claude's Cowork mode](https://claude.ai). It handles the full pipeline: automated discovery across multiple job boards, structured evaluation with fit scorecards, tailored resume generation with fabrication safeguards, and pipeline tracking.

This isn't a chatbot wrapper or a prompt template. It's an operable system with three purpose-built skills, a scoring rubric, an experience library that serves as a single source of truth, and a changelog-based audit trail that prevents AI hallucination from ending up on your resume.

## What It Does

**Discovery** -- Scans Indeed (via MCP), Google Jobs, and LinkedIn simultaneously. Deduplicates across sources. Applies light filtering against your scoring rubric. Populates a discovery queue with a 7-day TTL. Supports company watchlists for targeted career page scanning.

**Evaluation** -- Takes a job posting (from the queue, a URL, or pasted text) and produces a Fit Scorecard: skills match percentage with library references, gap surfacing, red flag assessment, company research, track assignment, and a positioning recommendation. Gaps become a conversation, not a guess.

**Resume Tailoring** -- Selects and reorders achievements from your experience library based on the scorecard's track assignment. Mirrors JD language where truthful. Produces a .docx with a mandatory changelog documenting every selection, exclusion, and language adjustment. The changelog is the fabrication firewall -- nothing goes on a resume that can't be traced back to the library.

**Pipeline Tracking** -- CSV-based tracker updated at every stage transition. Supports follow-up timing, interview scheduling awareness, and status reporting.

## Architecture

The system uses parallel search tracks (e.g., "Marketing Ops" and "AI Operations") that you define in a scoring rubric. Each track has its own target titles, keywords, and positioning strategy. The resume tailor uses track assignments to determine which experience leads and which supports.

```
scoring-rubric.md          -- Search parameters, comp floor, red flags, deal-breakers
experience-library.md      -- Single source of truth for all resume content
discovery-queue.md         -- Active results (7-day TTL)
discovery-overflow.md      -- Adjacent roles that don't match core parameters
company-watchlist.md       -- Companies to scan directly every run
tracker.csv                -- Pipeline state
applications/
  [company-name]/
    scorecard.md            -- Fit evaluation
    resume-draft-v1.docx    -- Tailored resume
    cover-letter-v1.docx    -- Cover letter (on request)
    supplemental-questions.md
```

Three skills drive the workflow:

- **job-discovery** -- Automated multi-source scanning with deduplication and overflow routing
- **job-evaluator** -- Fit scorecard generation with gap surfacing and company research
- **resume-tailor** -- Achievement selection, JD language mirroring, changelog-audited .docx production

## Key Design Decisions

**No fabrication, ever.** The experience library is the only source for resume content. If a skill isn't documented there, it doesn't appear on a resume. The mandatory changelog creates a paper trail for every selection and language adjustment.

**Education is immutable.** The system will never alter education credentials. No added years, no substituted institutions. Copied exactly from the library every time.

**Gap surfacing is a conversation.** When a JD requires something not in the library, the evaluator flags it and asks: "Real gap, or missing from the library?" If you have the experience, you add it to the library. If you don't, it stays flagged.

**The rubric is the source of truth for parameters.** Search titles, comp floors, red flag keywords, and location preferences live in the scoring rubric, not hardcoded in skills. Change the rubric, change the behavior.

**All three sources run every scan.** Indeed, Google Jobs, and LinkedIn each catch roles the others miss. The system doesn't skip sources because it "found enough."

## Prerequisites

- **Claude Desktop** with Cowork mode
- **Indeed MCP** (for job search via API)
- **Claude in Chrome** extension (for LinkedIn, Google Jobs, career page scanning)
- **Gmail MCP** (optional, for follow-up email drafts)
- **Google Calendar MCP** (optional, for interview scheduling)
- **Docx skill** (for .docx resume/cover letter production)

## Setup

The setup guide walks you through everything step by step, including prompts you paste into Claude to build your experience library and configure your rubric collaboratively.

1. Copy the repo contents to a working folder on your machine
2. Copy template files from `templates/` into your working folder root
3. Install the three skill folders into your Claude skills directory
4. Work through `SETUP-GUIDE.md` with Claude to populate your experience library and scoring rubric
5. Paste the configured `PROJECT-INSTRUCTIONS.md` into your Claude Project's custom instructions
6. Run your first discovery scan: tell Claude "run discovery"

See [`SETUP-GUIDE.md`](SETUP-GUIDE.md) for the full walkthrough.

## Usage

Once configured, the system responds to natural language:

| Command | What Happens |
|---------|-------------|
| "Run discovery" / "Scan for jobs" | Executes full discovery protocol across all sources |
| "What's in the queue?" | Summarizes active discovery results |
| "Evaluate this" / [paste JD] | Produces a Fit Scorecard |
| "Go" / "Build the resume" | Generates tailored resume from experience library |
| "Cover letter" | Produces a cover letter for the current application |
| "Tracker status" | Summarizes pipeline state |
| "Any follow-ups due?" | Scans for overdue follow-up dates |
| "Prep me for [company]" | Interview preparation workflow |

## Customization

The framework is designed to be personalized:

- **Tracks** -- Define as many search tracks as your career spans. Most people need one or two.
- **Scoring rubric** -- Your comp expectations, location preferences, red flags, and deal-breakers. Update as you learn the market.
- **Experience library** -- Grows over time as gap surfacing reveals undocumented experience.
- **Company watchlist** -- Add companies from networking, news, or research for direct career page scanning.

## File Reference

| File | Purpose |
|------|---------|
| `SETUP-GUIDE.md` | Step-by-step onboarding guide with Claude prompts |
| `PROJECT-INSTRUCTIONS.md` | Master prompt for Claude Project custom instructions |
| `skills/job-discovery/SKILL.md` | Discovery scan protocol |
| `skills/job-evaluator/SKILL.md` | Fit scorecard protocol |
| `skills/resume-tailor/SKILL.md` | Resume/cover letter/supplemental protocol |
| `templates/experience-library.md` | Template for documenting your work history |
| `templates/scoring-rubric.md` | Template for search parameters and preferences |
| `templates/tracker.csv` | Pipeline tracker with column headers |
| `templates/discovery-queue.md` | Discovery results queue |
| `templates/discovery-overflow.md` | Overflow results for edge-case roles |
| `templates/company-watchlist.md` | Companies to track for future openings |

## Contributing

Issues and PRs welcome. If you've extended the framework with additional skills (interview prep, networking tracker, salary negotiation), I'd be interested to see them.

## License

MIT. See [LICENSE](LICENSE).

## Author

Built by [Cameron Corniuk](https://www.linkedin.com/in/cameron-corniuk).
