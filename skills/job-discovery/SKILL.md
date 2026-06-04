---
name: job-discovery
description: Runs daily job discovery scans across configured tracks using Indeed MCP, Google Jobs via Claude in Chrome, and LinkedIn. Populates the discovery queue with scored, tagged results. Purges stale entries. Routes overflow to a separate file. Trigger this skill when running a job scan, during Cowork scheduled discovery tasks, or when the user says "scan for jobs," "run discovery," "what's new on Indeed," "job search," or asks about the discovery queue state.
---

# Job Discovery -- Automated Search Engine

## Role

You are the front-end scanner for {{USER_NAME}}'s job search system. Your job is to find relevant roles across configured search tracks, apply light filtering, and deliver a curated queue for evaluation. You run daily via Cowork scheduled task and on-demand when {{USER_NAME}} asks. You are not an evaluator -- you surface candidates. The job-evaluator skill handles scoring.

## Architecture

- **Discovery runs write to:** `{{WORKING_DIR}}/discovery-queue.md`
- **Overflow (adjacent but non-matching roles) writes to:** `{{WORKING_DIR}}/discovery-overflow.md`
- **Scoring rubric (read-only reference):** `{{WORKING_DIR}}/scoring-rubric.md`
- **Experience library (read-only reference):** `{{WORKING_DIR}}/experience-library.md`
- **Tracker (read to avoid duplicates):** `{{WORKING_DIR}}/tracker.csv`
- **Company watchlist (direct-scan every run, flag matches):** `{{WORKING_DIR}}/company-watchlist.md`

## Tools

- **Indeed MCP** -- `search_jobs` for keyword/title searches. `get_job_details` for full JD when snippets are insufficient. `get_company_data` for quick company context.
- **Claude in Chrome** -- Required for Google Jobs and LinkedIn Jobs searches. Also used for company career pages, posting date verification, and department hiring pattern checks.
- **Web search** -- Supplemental discovery for niche job boards, company career pages, or specialized aggregators.

## Search Parameters

Parameters are defined in `scoring-rubric.md` under "Search Parameters." Read that file at the start of every discovery run. Do not hardcode parameters here -- the rubric is the source of truth.

The rubric defines:
- **Search tracks** (e.g., Track A, Track B) with target titles, keywords, location preferences, and level filters
- **Compensation floor** -- roles below this threshold are auto-skipped
- **Location preferences** -- remote, hybrid, and onsite preferences with geographic filters

## Discovery Run Protocol

**All three sources (Indeed, Google Jobs, LinkedIn) run on every discovery scan.** The only acceptable reason to skip a source is a hard technical failure (Claude in Chrome extension not connected, Indeed MCP not responding). "Time-constrained" and "already found enough" are not valid reasons to skip. Each source catches roles the others miss. Run all three, every time.

### Step 1: Housekeeping
1. Read `discovery-queue.md`
2. Purge entries older than 7 days (based on `date_found` field)
3. Read `tracker.csv` to get list of companies/roles already in the pipeline (avoid surfacing duplicates)
4. Read `company-watchlist.md` to get the list of companies {{USER_NAME}} is tracking for future openings. These companies get a direct career-page scan in Step 6 and any queue entry gets a `[WATCHLIST]` flag.

### Step 2: Indeed Search -- Track A
1. Run `search_jobs` for each Track A title search term (from rubric)
2. Filter by location preferences (from rubric)
3. For promising results, run `get_job_details` to pull the full JD
4. Apply light fit check against experience library tags -- does {{USER_NAME}} have relevant experience?
5. Check comp if listed -- below comp floor (from rubric) is an auto-skip
6. Add qualifying results to queue

### Step 3: Indeed Search -- Track B (and additional tracks)
1. Run `search_jobs` for each Track B title search term (from rubric)
2. Same filtering and light fit check as Track A
3. If a track has unstandardized titles, cast a wider net. If a result doesn't match core parameters but looks adjacent and potentially relevant, route it to `discovery-overflow.md` instead of discarding
4. Repeat for any additional tracks defined in the rubric

### Step 4: Google Jobs Search (Claude in Chrome)
Google Jobs aggregates listings from multiple job boards and company career pages, catching roles that may not appear on Indeed. This is a primary source, not a supplement.

1. For each track, navigate to Google Jobs using the search URL pattern:
   `https://www.google.com/search?q=[title+keywords]+[location]&ibp=htl;jobs`
2. Apply filters in the Google Jobs UI:
   - Location: Match rubric location preferences (Remote toggle, geographic filters)
   - Date posted: Past week (aligns with the 7-day queue TTL)
3. Scan results for each track's title search terms
4. For promising results: click through to the full listing to get the actual job posting URL
5. Apply the same light fit check and comp floor filter as Indeed steps
6. Note the source as "Google Jobs" in the queue entry -- include the original posting source if visible (e.g., "Google Jobs -> Company career page", "Google Jobs -> LinkedIn")

**Google Jobs search tips:**
- Google Jobs deduplicates across boards, so a single result may link to Indeed, LinkedIn, Glassdoor, and the company page. Prefer the company career page link when available.
- Use the "Date posted" filter aggressively -- stale results waste time.
- If a result already appeared in the Indeed steps, skip it (dedup in Step 7 will also catch this, but skipping early saves time).

### Step 5: LinkedIn Jobs (Claude in Chrome)
1. Navigate to LinkedIn Jobs search
2. Search all track title terms with location filters from the rubric
3. Focus on results NOT already caught by Indeed or Google Jobs -- LinkedIn's value is roles posted exclusively on the platform and roles from {{USER_NAME}}'s network
4. Spot-check company career pages for roles that may not be syndicated anywhere

### Step 6: Watchlist Company Direct Scan
For each company in `company-watchlist.md`:

1. Visit the company's careers page directly (via Claude in Chrome)
2. Search for openings matching {{USER_NAME}}'s target titles and levels across all tracks
3. If a qualifying role is found:
   - Add it to `discovery-queue.md` with `[WATCHLIST]` prepended to the Light Fit description
   - Elevate it to the top of the queue regardless of source order
4. If no qualifying role is posted, note the check in the run summary. Do not remove companies from the watchlist -- retire criteria are managed inside `company-watchlist.md`.

Any role caught in Steps 2-5 for a watchlist company also gets the `[WATCHLIST]` flag and elevated placement.

### Step 7: Capture Listing URLs
**Every queue entry MUST include a working URL to the actual job listing.** No exceptions. If you cannot get a URL for a role, do not add it to the queue.

For each result found across all sources:
1. Get the direct URL to the job posting. Click through from search results to the actual listing page.
2. Prefer company career page URLs when available (most stable, most complete)
3. Indeed URLs from `get_job_details` are acceptable
4. LinkedIn URLs are acceptable but note they may require login for full access
5. Google Jobs URLs (the google.com/search URLs) are NOT acceptable as the queue URL -- they are search results, not listings. Always click through to the actual posting.

**If a listing has no reachable URL, do not add it to the queue.** {{USER_NAME}} needs to be able to click and verify every result.

### Step 8: Deduplication
1. Compare new finds against existing queue entries (match on company + title)
2. Compare against tracker.csv (match on company + similar title)
3. Cross-source dedup: if the same role appeared on Indeed, Google Jobs, and LinkedIn, keep one entry. Prefer the source with the most complete JD information. Note all sources found in the entry.
4. Do not surface duplicates

### Step 9: Write Results
1. Append new entries to `discovery-queue.md` in the format below
2. Route overflow entries to `discovery-overflow.md`

## Discovery Queue Entry Format

Each entry in `discovery-queue.md` follows this structure:

```markdown
---

### [Job Title] -- [Company Name]
- **Track:** [Track name from rubric]
- **URL:** [REQUIRED -- direct link to the actual job listing, not a search results page]
- **Source:** Indeed | Google Jobs | LinkedIn | Career Page | Other [note if found on multiple: "Google Jobs + Indeed"]
- **Date Found:** YYYY-MM-DD
- **Location:** Remote | Hybrid -- [City, ST] | Onsite -- [City, ST]
- **Comp (if listed):** [range or "Not listed"]
- **Light Fit:** [prepend `[WATCHLIST]` if the company is on `company-watchlist.md`, then 1-2 sentence quick assessment]
- **Red Flags (quick scan):** [Any obvious flags from the posting -- "describes 3 jobs," "posted 90 days ago," etc. Or "None visible."]
```

## Overflow Entry Format

Same as above, but with an additional field:

```markdown
- **Overflow Reason:** [Why this didn't match core parameters but looked potentially relevant]
```

## Status Output

After each run, output a summary that {{USER_NAME}} can act on without searching. Every role mentioned anywhere in the summary must include **company name + job title + clickable URL**. Bare company names or titles without URLs are useless -- {{USER_NAME}} needs to click and verify.

```
DISCOVERY RUN -- [Date]

**Sources:** Indeed [checkmark/x] | Google Jobs [checkmark/x] | LinkedIn [checkmark/x] [use x and reason for any not run]

**Stale Purged:** [count] -- [Title -- Company](URL) for each purged entry
**Duplicates Skipped:** [count] [note cross-source dupes separately if significant]

**New to Queue ([count]):**
- [Title -- Company](URL) | Track [name] | [Location] | [Comp or "Comp not listed"] | [1-line light fit note]
[repeat for each new entry; mark standouts with star]

**New to Overflow ([count]):**
- [Title -- Company](URL) | [Overflow reason in <10 words]
[repeat for each overflow entry]

**Watchlist Scan:** [N companies checked; M matches found]
- [Company]: [result -- e.g., "No qualifying roles" or "[Title](URL) -- promoted to queue"]

**Queue Total:** [count] active entries
```

The key principle: if {{USER_NAME}} cannot click a URL for every role in the summary, the summary is incomplete. No bare company names. No bare titles. Every mention = company + title + URL.

## Source Priority

When the same role appears on multiple sources, keep one entry using this priority for the URL:
1. **Company career page** (most authoritative, often has the most complete JD)
2. **Indeed** (structured data via MCP, comp estimates available)
3. **Google Jobs** (aggregated, may link to career page)
4. **LinkedIn** (useful for network context but often requires login for full details)

Note all sources found in the entry's Source field regardless of which URL is used.

## Calibration

- If discovery runs consistently return 0 results for a track, broaden keyword terms and log the change in `scoring-rubric.md` under Parameter Maintenance.
- If discovery runs return 20+ results per track, tighten filters (comp floor, location, level) and note the adjustment.
- After every 5 discovery runs, briefly note whether the search parameters feel calibrated or need adjustment. {{USER_NAME}} makes the final call on parameter changes.
- Track which sources are producing the most unique results (roles not found on other sources). After 10 runs, report source contribution breakdown.

## Rules

1. **Never evaluate.** Light fit checks only. The job-evaluator skill does the real scoring.
2. **Never apply.** Discovery surfaces candidates. {{USER_NAME}} decides what to pursue.
3. **Purge aggressively.** 7-day TTL on queue entries. Stale listings waste attention.
4. **Overflow is a feature.** Emerging or unstandardized tracks benefit from overflow. {{USER_NAME}} reviews overflow weekly.
5. **Duplicates are noise.** Check queue and tracker before adding anything. Cross-source dedup is essential.
6. **Read the rubric every run.** Parameters may have changed since last scan.
7. **Run all three sources every scan.** Indeed, Google Jobs, LinkedIn. No skipping unless a source is technically unavailable. "Already found enough" is not a reason to skip.
8. **Every entry needs a clickable URL.** No URL, no queue entry. {{USER_NAME}} must be able to verify every result.
9. **Watchlist companies get a direct career-page scan every run.** Any match is flagged `[WATCHLIST]` and elevated to the top of the queue regardless of source. The watchlist is a long game -- keep it warm with monthly direct checks minimum, every-run checks by default.
