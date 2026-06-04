# Job Search System -- Setup Guide

This is an AI-powered job search system designed to run inside Claude's Cowork mode. It handles discovery, evaluation, resume tailoring, and pipeline tracking through a structured set of skills and reference files.

This guide walks you through setting it up for your specific situation. **You'll do this WITH Claude** -- paste the setup prompts below into a Claude conversation and work through them together.

---

## What You're Setting Up

The system has three layers:

1. **Reference files** -- Your experience library (the single source of truth for all resume content), scoring rubric (search parameters, comp preferences, red flags), and pipeline tracker.
2. **Skills** -- Three automated workflows: job discovery (finds roles), job evaluator (scores them), and resume tailor (builds application materials).
3. **Project instructions** -- The master prompt that tells Claude how the system works. Gets pasted into your Claude Project's custom instructions.

---

## Prerequisites

- **Claude Desktop app** with Cowork mode
- **Indeed MCP** connected (for job search)
- **Claude in Chrome** extension (for LinkedIn, Google Jobs, career pages)
- **Gmail MCP** connected (optional, for follow-up email drafts)
- **Google Calendar MCP** connected (optional, for interview scheduling)
- A folder on your computer for the system files (e.g., `Documents/job-search/`)

---

## Step 1: Set Up Your Folder Structure

Create this folder structure (or let Claude do it):

```
job-search/
  experience-library.md
  scoring-rubric.md
  discovery-queue.md
  discovery-overflow.md
  company-watchlist.md
  tracker.csv
  applications/
```

Copy the template files from the `templates/` folder in this package into your `job-search/` folder.

---

## Step 2: Install the Skills

Copy the three skill folders from this package into your Claude skills directory:

- `skills/job-discovery/` -> your Claude skills folder
- `skills/job-evaluator/` -> your Claude skills folder
- `skills/resume-tailor/` -> your Claude skills folder

The exact path depends on your OS and Claude installation. On Windows, skills typically live under:
`%APPDATA%\Claude\local-agent-mode-sessions\skills-plugin\[session-id]\skills\`

If you're unsure, ask Claude: "Where are my skill files stored?" and it will find the path.

---

## Step 3: Build Your Experience Library

This is the most important step. The experience library is the ONLY source for resume content. Nothing goes on a resume that isn't documented here.

**Paste this prompt into a Claude conversation:**

```
I'm setting up my job search system. I need to build my experience library.

Here's my background: [paste your current resume, or describe your work history]

For each role I've held, I need you to:
1. List the company, title, dates, and location
2. Break my achievements into individual numbered entries with metrics where possible
3. Tag each achievement with relevant skills/keywords
4. Pull out my technical skills into categories
5. Document my education exactly as it should appear on a resume
6. List any certifications

Use the format in experience-library.md. Number achievements sequentially across all roles.

After we build the initial library, I'll review and we can add, remove, or refine entries.
```

**Tips:**
- Be thorough. The more you put in now, the better the system works.
- Include metrics wherever possible (revenue influenced, percentage improvements, team size, tools implemented).
- If you have achievements you're not sure about including, add them -- you can always exclude them per-role during tailoring.
- Include gap coverage roles (freelance work, side projects, sabbaticals) if they fill timeline gaps.

---

## Step 4: Configure Your Scoring Rubric

The rubric defines what you're looking for: titles, keywords, comp expectations, red flags, and deal-breakers.

**Paste this prompt into a Claude conversation:**

```
I'm configuring my job search scoring rubric. Here's what I'm looking for:

**What I do / want to do:** [describe your target roles in plain language]

**Tracks:** I want to search across these tracks:
- Track A: [name and description, e.g., "Data Engineering -- looking for senior/lead IC roles building data pipelines"]
- Track B: [name and description, or say "just one track" if that's your situation]

**Comp:** My floor is $[X]. My target range is $[X]-$[Y].

**Location:** [Remote only / open to hybrid in [city] / etc.]

**Level:** [IC, Manager, Director, VP -- what are you targeting?]

**Industries I like:** [list]
**Industries I avoid:** [list, if any]

**Red flags I've learned to watch for:** [anything from past experience -- e.g., "companies that list 15 required skills," "roles that have been posted for 3+ months"]

**Deal-breakers:** [absolute no-go criteria]

Please populate my scoring-rubric.md with this information in the template format.
```

---

## Step 5: Configure the Project Instructions

Open `PROJECT-INSTRUCTIONS.md` and replace all `{{PLACEHOLDER}}` values:

- `{{USER_NAME}}` -- your name
- `{{NUMBER_OF_TRACKS}}` -- how many search tracks you configured (usually 1-2)
- `{{TRACK_DESCRIPTIONS}}` -- one line per track describing the positioning strategy
- `{{WORKING_DIR}}` -- the full path to your job-search folder
- `{{SKILLS_DIR}}` -- the full path to where you installed the skill folders

Then copy the entire contents into your Claude Project's custom instructions.

---

## Step 6: Update Skill File Paths

Open each skill file and replace `{{PLACEHOLDER}}` values:

- `{{USER_NAME}}` -- your name
- `{{WORKING_DIR}}` -- the full path to your job-search folder

---

## Step 7: First Discovery Run

Once everything is configured, test the system:

```
Run discovery
```

Claude will:
1. Read your rubric for search parameters
2. Search Indeed, Google Jobs, and LinkedIn
3. Populate your discovery queue
4. Give you a summary of what it found

Review the results. If the parameters are too broad (too many irrelevant results) or too narrow (nothing found), adjust the rubric and run again.

---

## Step 8: First Evaluation

Pick a role from the queue and say:

```
Evaluate [Company -- Title]
```

Claude will produce a Fit Scorecard. Review it. This is where you'll discover if your experience library has gaps -- the evaluator will surface anything the JD requires that isn't documented in your library. Fill those gaps as they come up.

---

## Day-to-Day Usage

Once set up, the workflow is:

1. **"Run discovery"** -- daily or every few days. Claude scans and populates the queue.
2. **"What's in the queue?"** -- review what's been found.
3. **"Evaluate this"** -- pick interesting roles for full scorecards.
4. **"Go" / "Build the resume"** -- after a positive scorecard, get tailored materials.
5. **"Cover letter"** -- when needed.
6. **"Tracker status"** / **"Any follow-ups due?"** -- keep the pipeline moving.
7. **"Prep me for [company]"** -- before interviews.

---

## Maintenance

- **Experience library:** Update whenever you discover a gap during evaluation, or when you want to add new achievements.
- **Scoring rubric:** Adjust search parameters if discovery runs are returning too many or too few results. Update comp expectations as you learn the market.
- **Company watchlist:** Add companies you hear about through networking, news, or research.
- **Tracker:** The system maintains this automatically. Review periodically to make sure nothing is stale.

---

## Customization Ideas

- **Add more tracks** if your search spans more than two domains
- **Add an interview-prep skill** for deep company research, STAR story selection, and question prep
- **Schedule discovery runs** via Cowork's scheduling feature so they run automatically
- **Connect Google Drive MCP** to sync tracker to Google Sheets for mobile access
- **Add a follow-up email skill** with templates for different stages (post-application, post-interview, negotiation)
