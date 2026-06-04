# Scoring Rubric

> **Read this file at the start of every task.** This is the source of truth for search parameters, compensation preferences, red flags, deal-breakers, and scoring logic.
>
> Use the Setup Guide to configure this file with Claude's help.

---

## Search Parameters

### Track A -- {{TRACK_A_NAME}}
> Example: "Marketing Operations / RevOps"

**Target Titles:**
- [Title 1]
- [Title 2]
- [Title 3]

**Keywords:**
- [keyword1, keyword2, keyword3]

**Location:**
- Primary: [Remote / specific city / state]
- Secondary: [fallback location preference]

**Level:**
- [e.g., Mid-senior / Manager / Director]

---

### Track B -- {{TRACK_B_NAME}}
> Example: "AI Operations / AI Systems"

**Target Titles:**
- [Title 1]
- [Title 2]
- [Title 3]

**Keywords:**
- [keyword1, keyword2, keyword3]

**Location:**
- Primary: [Remote / specific city / state]
- Secondary: [fallback location preference]

**Level:**
- [e.g., Mid-senior / Manager / Director]

---

> Add more tracks if needed. Two is a good starting point. Some people only need one.

---

## Compensation

**Floor:** $[amount] -- roles below this are auto-skipped during discovery
**Target range:** $[low] -- $[high]
**Strong range:** $[amount]+

**Assessment scale:**
- **Strong:** Above target range
- **Acceptable:** Within target range
- **Weak:** Between floor and target low
- **Pass:** Below floor

---

## Location Preferences

**Assessment scale:**
- **Strong:** [e.g., "Fully remote"]
- **Acceptable:** [e.g., "Hybrid with 1-2 days/week, within commute range"]
- **Weak:** [e.g., "Hybrid 3+ days, long commute"]
- **Pass:** [e.g., "Full onsite or relocation required"]

**Geographic preferences:** [City/state/region you're open to]
**Hard no locations:** [Places you won't consider]

---

## Red Flag Taxonomy

### Hard Red Flags (any one of these = serious concern)
- [e.g., "Describes 3+ distinct jobs in one JD"]
- [e.g., "Posted 90+ days with no repost -- something is wrong"]
- [e.g., "Comp significantly below floor"]
- [e.g., "'Fast-paced' + 'wear many hats' + startup with no funding clarity"]
- [Add your own based on experience]

### Soft Red Flags (note and monitor, not auto-disqualify)
- [e.g., "Glassdoor below 3.0 overall"]
- [e.g., "Multiple similar roles posted simultaneously"]
- [e.g., "Vague reporting structure"]
- [Add your own]

### Hard Red Flag Keywords (in JD text)
- [e.g., "unlimited PTO" -- often means no PTO]
- [e.g., "family" culture framing]
- [Add keywords that signal problems in your industry]

### Soft Red Flag Keywords (in JD text)
- [e.g., "rockstar," "ninja," "guru"]
- [e.g., "fast-paced environment"]
- [Add your own]

---

## Deal-Breakers

> These are absolute no-go criteria. If any are present, the verdict is Pass regardless of other factors.

- [e.g., "Requires relocation to [specific place]"]
- [e.g., "Industry: [specific industry you won't work in]"]
- [e.g., "Comp below $X with no equity/upside"]
- [Add your own]

---

## Fit Score Calculation

**Strong Fit:**
- 80%+ skills match
- Comp in range (Acceptable or Strong)
- No hard red flags
- Clear track assignment
- Clear positioning angle

**Worth a Shot:**
- 60-79% skills match
- OR comp borderline (Weak)
- OR 1 hard flag that might not be a dealbreaker
- OR gaps exist but are addressable

**Pass:**
- Below 60% skills match
- OR comp below floor
- OR 2+ hard red flags
- OR fundamental misalignment with career goals

### Override Rules
> Conditions where the system should bump a verdict up or down regardless of the raw score.

- [e.g., "Company on watchlist bumps Worth a Shot -> Strong Fit for track-matched roles"]
- [e.g., "Dream company exception: evaluate even if skills match is 50-59%"]
- [Add your own override logic]

---

## Follow-Up Timing

- **Post-application:** [e.g., 7 business days]
- **Post-interview:** [e.g., 1 business day thank-you, 5 business days status check]
- **Post-offer:** [e.g., 2-3 business days response window]

---

## Parameter Maintenance

> Log changes to search parameters here so you can track what shifted and why.

| Date | Parameter Changed | Old Value | New Value | Reason |
|------|-------------------|-----------|-----------|--------|
| YYYY-MM-DD | [parameter] | [old] | [new] | [why] |
