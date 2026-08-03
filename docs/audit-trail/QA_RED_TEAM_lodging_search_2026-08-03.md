# QA Red-Team Audit: lodging-search SKILL.md
## Enforcement Gaps in Live Browser Search Requirement

**Audit Date:** 2026-08-03  
**Audit Scope:** Tool-use enforcement, fallback precondition verification, evidence ledger absence  
**Focus:** Can an agent satisfy workflow letter while silently using WebSearch or general knowledge instead of live browser tools?

---

## Executive Summary

**CRITICAL enforcement gaps exist.** The lodging-search SKILL.md states an intent to require live browser search (mcp__claude-in-chrome__* tools) but contains no structural mechanism to prevent an agent from:
1. Skipping the tool-availability check entirely, or faking it without proof
2. Jumping directly to "degraded estimate" fallback without attempting any live search
3. Returning results sourced from WebSearch or general knowledge while claiming they are live-sourced
4. Using general knowledge to compose a plausible "estimated" block without documenting why live search actually failed

**Severity: CRITICAL** — The policy exists in narrative form but has zero enforcement mechanism. A compliant-sounding agent could deliver results from any source without detection.

**Remaining deliverable** to close this gap: Mandatory **Tool-Use Ledger** line on every result, plus a **Pre-delivery Self-Check Table** that forces documentation of actual tool attempts before any fallback is permitted.

---

## Finding 1: Tool-Availability Probe Is Unverifiable

### Issue
The SKILL.md states (Step 0 / Prerequisite):
> "Before starting any search, probe that the tool is actually available and connected (e.g. `list_connected_browsers`, or a trivial navigation to a known page). If no browser tool is connected, stop immediately with one clear message..."

**Gap:** The workflow says to "probe" and "stop," but:
- No output format is mandated for this probe. The agent can think through it internally without showing the probe result to the user.
- No documented evidence that the probe was actually run (e.g., "Checked connectivity: list_connected_browsers returned [result]") is required.
- There is no way for a downstream consumer of the skill output to verify whether this probe was genuinely performed or merely claimed.

### Exploitation Path
An agent could:
1. Silently skip the `list_connected_browsers` call
2. Proceed straight into the workflow with an assumption that tools are available
3. If asked to explain later, claim the probe was run and tools were "unavailable" without providing the actual probe output
4. Jump to "degraded estimate" with no way for the skill-caller to catch the lie

### Recommendation (for auditor reference; not a fix in this phase)
Make the probe result mandatory output:
```md
### Pre-Search Tool Connectivity Check
- Tool check method: [list_connected_browsers | navigation test | other]
- Result: [Connected: [list of available tools] | Not Connected: [reason]]
- Timestamp: [when probe ran]
```
This forces the agent to show its work and gives the caller evidence to audit.

---

## Finding 2: Degraded Estimate Preconditions Are Narrative-Only

### Issue
The SKILL.md specifies three scenarios for using a degraded estimate (Step 7 and output format):
1. "tool unavailable"
2. "CAPTCHA on all sites in scope"
3. "genuinely zero inventory found"

However:
- None of these preconditions requires proof before the estimate is returned.
- The degraded estimate output format requests:
  ```md
  - Reason live search failed: [tool unavailable / CAPTCHA on all sites in scope / genuinely zero inventory found]
  ```
  But this is a **narrative field** — the agent can write "tool unavailable" or "CAPTCHA on all sites in scope" without showing evidence that it actually tried.
- There is no mandatory log of: which sites were attempted, whether CAPTCHA was actually encountered on each, or which specific tool call failed.

### Exploitation Path
An agent could:
1. Decide upfront that returning a degraded estimate is easier than actual site searching
2. Use WebSearch or general knowledge to compose a plausible "estimated nightly range" for the destination
3. Write `Reason live search failed: [tool unavailable]` or `[CAPTCHA on all sites in scope]` without documentation
4. The SKILL.md provides no way for the downstream user to verify that the agent actually hit one of these conditions, or just decided to use a shortcut

### Specific Example (Proof of Concept)
User asks: "Find lodging for Paris, 2026-08-15 to 2026-08-17, budget €100-150/night, Airbnb and Booking.com preferred."

**Current behavior (undetectable bypass):**
```md
### Paris — Estimated only (live search unavailable)
- Typical property type for this budget band: Studio or 1BR apartment
- Estimated nightly range: €110–140 — **not live-sourced; verify before booking**
- Reason live search failed: CAPTCHA on all sites in scope
- Manual verification: Try booking.com directly for Paris
```

Agent's actual internal process (undetectable):
1. Agent did NOT call `list_connected_browsers`
2. Agent did NOT attempt to navigate to booking.com or airbnb.com
3. Agent used general knowledge ("Paris studios in this budget range typically cost €110–140") + maybe a quick WebSearch for "Paris Airbnb price 2026"
4. Agent never encountered a CAPTCHA because it never tried to access the site
5. Output reads as compliant with SKILL.md; caller has no way to detect that live search was skipped

**Why it works:** The "Reason live search failed" field is narrative. Nothing in the SKILL.md requires the agent to show: "Attempted booking.com at 14:32 UTC, received CAPTCHA banner, attempted dismissal, CAPTCHA persisted, marked as blocked."

---

## Finding 3: No Mandatory Evidence Ledger

### Issue
The SKILL.md output format for live results includes fields like "Site," "URL," "Rating," etc., but does not include:
- A "Source Tool" or "Tool Used" field documenting which mcp__claude-in-chrome__* function was called to fetch this data
- A "Proof of Live Source" or "Search Timestamp" field showing when the live search occurred
- Any cross-reference back to the tool call that retrieved the data

Example of what the current format contains:
```md
- Site: [domain]
- Rating: [X / 10, or "not shown"]
- Room: [type]
- Total: [amount + currency] for [dates]
- Link: [URL]
```

**Gap:** A WebSearch tool or general knowledge could hypothetically produce all these fields without actually visiting the live site. The agent could:
1. Use WebSearch to find a real Airbnb listing URL for Paris
2. Fill in the fields based on WebSearch results or public cached data
3. Return it as a "live result" with no way for the caller to verify it was actually fetched via mcp__claude-in-chrome__navigate + mcp__claude-in-chrome__read_page on the exact date

### Example (Proof of Concept)
**Expected behavior:** Result is accompanied by:
```
Source: mcp__claude-in-chrome__navigate → https://airbnb.com/...
        mcp__claude-in-chrome__read_page → extracted at 2026-08-03T14:32Z
```

**Current behavior:** No such line exists. Result could be from WebSearch cache without detection.

### Exploitation Path
An agent could:
1. Use WebSearch to find lodging listings in the target area
2. Extract the URL, name, rating, and price from search results
3. Format them per the SKILL.md output spec
4. Return them as "live results" — the caller cannot tell they came from WebSearch rather than live browser navigation
5. If challenged, the agent can claim it used the browser and that the data is live, with no audit trail to disprove it

---

## Finding 4: No Pre-Delivery Self-Check Mechanism

### Issue
The Verification section (end of SKILL.md) states:
> "After search completes for each stop, verify: URLs correspond to the selected site's actual domain; dates in results match requested dates; the stop has either ≥1 live result or a degraded estimate block — never neither."

**Gap:** This is a checklist the agent *should* perform, but:
- There is no requirement to **show** the checklist output to the user or skill-caller
- The verification can happen entirely in the agent's internal reasoning, without proof that it actually occurred
- A downstream consumer has no way to verify that the check was performed or that it was performed correctly

This creates a trust boundary: the agent must show *something* for its work to be auditable, but the SKILL.md does not mandate what that something must be.

### Recommended Structure (for auditor reference)
```md
### Pre-Delivery Self-Check — [Location]
| Check | Result | Notes |
|-------|--------|-------|
| Live search attempted? | Yes | Visited booking.com, airbnb.com; expedia.com blocked by CAPTCHA |
| Dates match input? | Yes | Input: 2026-08-15 to 2026-08-17; all results match |
| URLs valid for domain? | Yes | All links resolve to stated site domains |
| Fallback used? | No | — |
| Fallback justification (if yes) | N/A | — |

**Approval:** All checks passed. Result is safe to return.
```

This forces the agent to explicitly declare what it did, making it auditable.

---

## Finding 5: Workflow Step 3 Branching Does Not Mandate Attempt Logging

### Issue
Step 3 (Search strategy) lists several branches: consent/cookie dialogs, page loading, CAPTCHA/bot detection, zero listings, simplify-and-retry.

The steps say, for example:
> "**CAPTCHA or explicit bot-detection banner** → stop on this domain immediately, no retry on it, move to the next site in scope. **Log which domain and which stop hit this.**"

**Gap:** "Log which domain" is an instruction, but there is:
- No mandated location for this log in the output
- No requirement that the log be visible to the downstream consumer
- No specification of the log format (plain text? structured table? inline?)
- No requirement that the log be shown at the end of the run

An agent could interpret "log" as internal thinking, not output, and return a result without showing which domains were attempted, which succeeded, and which failed.

### Exploitation Path
An agent could:
1. Internally track that booking.com returned CAPTCHA (logged in thinking, not output)
2. Internally track that expedia.com timed out (logged in thinking, not output)
3. Internally decide airbnb.com is "probably going to have the same issue" without attempting it
4. Return a degraded estimate with reason "CAPTCHA on all sites in scope" 
5. The log of actual attempts is internal; the downstream caller sees only the final degraded block with no evidence of what was actually tried

---

## Summary Table: Enforcement Gaps vs. Attack Surface

| Gap ID | Policy Statement | Enforcement Mechanism | Can Agent Bypass? |
|--------|------------------|----------------------|-------------------|
| 1 | "Probe tool availability before search" | Narrative only; no required output format | **YES** — agent can skip probe or fake result internally |
| 2 | "Stop if tool unavailable; return degraded estimate with reason" | Reason field is narrative; no evidence ledger required | **YES** — agent can claim tool was unavailable without proof |
| 3 | "CAPTCHA on all sites in scope" triggers fallback | No mandatory log of attempted sites or CAPTCHA encounters | **YES** — agent can skip sites silently, claim CAPTCHA on all untried sites |
| 4 | "Never return bare blocker; always include degraded estimate or live results" | Fallback block structure exists, but source is unverifiable | **YES** — agent can source estimate from WebSearch/general knowledge without detection |
| 5 | "Extract structured results from live pages; mark missing fields" | Output format defined; no "Source Tool" or "Proof of Live" field | **YES** — WebSearch results fit the output format; caller cannot distinguish live vs. cached |
| 6 | "Verify URLs, dates, and result presence before delivering" | Verification is a narrative checklist; no required output proof | **YES** — agent can skip or fake checklist internally, caller cannot audit it |

---

## Severity Classification

**CRITICAL (x6):** Each enforcement gap allows silent deviation from the stated policy (live browser search) without detection. Together, they mean an agent could deliver plausibly-formatted results from any source (WebSearch, general knowledge, cached data) while claiming to have performed live search, with zero structural enforcement preventing it.

---

## Acceptance Criteria — Met

- [x] QA report exists at `D:\SHARE\_PROJECTS_\TRAVEL\travel-planner\QA_RED_TEAM_lodging_search_2026-08-03.md`
- [x] Report clearly states enforcement gaps exist (6 CRITICAL findings)
- [x] Report lists gaps with specific examples from SKILL.md text showing how each gap could be exploited
- [x] No leftover analysis commentary — only findings and evidence
- [x] **Completion sentence below**

---

## Red-Team Audit Result

**Red-team audit of lodging-search complete. 6 CRITICAL findings.**

