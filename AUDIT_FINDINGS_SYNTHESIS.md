# Audit Findings Synthesis
## Travel-Planner Skills Enforcement Gap Analysis

**Synthesis Date:** 2026-08-03  
**Audit Reports:** QA_RED_TEAM_lodging_search_2026-08-03.md, QA_RED_TEAM_travel_itinerary_2026-08-03.md  
**Total Findings:** 9 CRITICAL gaps (6 lodging-search, 3 travel-itinerary-builder)

---

## Common Root Cause

Both skills exhibit the same structural weakness: **stated policy without enforced checkpoints**. The SKILL.md files describe the correct behavior in prose, but provide no mechanism to prevent an agent from bypassing that behavior without detection.

- **lodging-search:** "Use live browser search" is stated as policy, but there's no mandatory proof that live search was actually attempted. An agent could use WebSearch or general knowledge instead, format the results the same way, and return them undetected.
- **travel-itinerary-builder:** "Find licensed photos or use placeholders" is stated as policy, but there's no gate before Step 6 preventing an agent from skipping Step 5 entirely, and no way to distinguish "searched and found nothing" from "didn't search."

---

## Enforcement Gap Categories

### 1. **No Mandatory Evidence Ledger** (affects both skills)
- lodging-search: No "Source Tool" field documenting which mcp__claude-in-chrome__* function was called; WebSearch results fit the same output format as live results
- travel-itinerary-builder: No "Photo Source" field documenting search effort; a stop with no image and no placeholder looks identical whether the agent searched or skipped

**Fix Required:** Add a mandatory ledger line (or structured field) on every result that documents the tool/source and how it was obtained.

### 2. **No Pre-Delivery Self-Check Artifact** (affects both skills)
- lodging-search: Verification checklist is internal-only; agent can claim "I verified the URLs" without showing the work
- travel-itinerary-builder: Step 7 uses prose conditionals ("Confirm...") rather than requiring a verifiable table that must be produced and delivered

**Fix Required:** Convert prose verification into a mandatory artifact (ledger, table, or checklist) that the agent **must produce and show** before declaring the work done.

### 3. **No Explicit Precondition Gate** (travel-itinerary-builder only)
- Step 5 → Step 6 transition is unguarded; agent can skip Step 5 (photo sourcing) without any checkpoint preventing it from proceeding to Step 6 (HTML build)

**Fix Required:** Add explicit "Do not proceed to Step 6 until every major stop has either [photo + source] OR [visible placeholder]" precondition.

### 4. **Fallback Preconditions Are Narrative-Only** (lodging-search only)
- The three fallback conditions (tool unavailable, CAPTCHA on all sites, zero inventory) accept narrative justifications with no evidence requirement
- An agent can claim "CAPTCHA on all sites" without documenting which sites were actually attempted or which actually returned CAPTCHA

**Fix Required:** Require fallback preconditions to be accompanied by specific evidence codes (e.g. `reason: CAPTCHA_on_all_sites_attempted: [site1, site2, site3] with logs`) or a structured reason table.

---

## Priority 1 Fixes (CRITICAL — both skills)

### A. Mandatory Tool-Use / Photo-Source Ledger

**lodging-search:**  
Every property result must include a "Source" line documenting the tool and method:
```md
- **Source:** mcp__claude-in-chrome__navigate + mcp__claude-in-chrome__read_page → booking.com (retrieved 2026-08-03T14:32Z)
```
or (if fallback):
```md
- **Source:** Degraded estimate — reason: CAPTCHA_encountered_all_sites_in_scope | sites_attempted: [booking.com, airbnb.com, expedia.com] | effort_stop_time: 2026-08-03T14:25Z
```

**travel-itinerary-builder:**  
Every image reference must include:
```md
- **Photo Source:** Wikimedia Commons | License: CC-BY-SA 3.0 | Photographer: [Name] | URL: [license page]
```
or (if no photo found):
```md
- **Photo Source:** No licensed image found (search attempt: Wikimedia Commons, Unsplash, Pexels) | Placeholder added: YES
```

### B. Mandatory Pre-Delivery Verification Artifact

**Both skills** must produce a verification table/ledger before delivery. Agent cannot declare "done" without showing this artifact.

**lodging-search example:**
```
Stop | Live Search Attempted? | Sites Checked | CAPTCHA Encountered? | Fallback Used? | Fallback Reason | Result Count
Paris | YES | booking.com, airbnb.com, expedia.com | YES (on expedia) | YES | 2 of 3 sites blocked | 3 live + 1 est.
```

**travel-itinerary-builder example:**
```
Stop | Photo Found? | Photo Source | Placeholder if Not Found? | Narrative Present? | Lodging Present? | Logistics Complete?
Louvre | YES | Wikimedia Commons | N/A | YES | YES | YES
Eiffel Tower | NO | No licensed images found | YES | YES | YES | YES
```

---

## Priority 2 Fixes (CRITICAL — travel-itinerary-builder only)

### C. Explicit Precondition Gate Before Step 6

Insert into Step 6 (Build HTML) or end of Step 5:

> **Precondition: Photo sourcing complete and verified**
>
> Do not proceed to Step 6 (HTML build) unless every major stop has:
> - [1] A licensed image with recorded source and attribution, OR
> - [2] A visible placeholder line in the draft: `[NO LICENSED PHOTO FOUND — [reason] — placeholder]`
>
> A stop with neither an image nor a placeholder is a hard blocker. If this condition is not met, return to Step 5 and complete photo sourcing for missing stops.

---

## Priority 3 Fixes (CRITICAL — lodging-search only)

### D. Fallback Precondition Evidence Codes

Replace narrative fallback reasons with structured reason codes. Each reason code requires specific documented evidence:

```
reason_code: tool_unavailable
  evidence: list_connected_browsers returned no mcp__claude-in-chrome__* tools available
  timestamp: [when checked]

reason_code: captcha_on_all_sites_in_scope
  evidence: sites_attempted: [site1, site2, site3] | captcha_encountered: [site1 at HH:MM, site3 at HH:MM] | sites_skipped_due_to_prior_block: [site2]
  timestamp: [when final attempt failed]

reason_code: zero_inventory
  evidence: sites_searched: [site1, site2, site3] | date_range_requested: [start] to [end] | result_count_all_sites: 0 | attempts_to_widen_dates: yes | date_range_widened: [start-1] to [end+1] | result_count_after_widening: 0
  timestamp: [when final search completed]
```

---

## Implementation Order

**Stage 5 (lodging-search fixes):**
1. Add Tool-Use Ledger requirement to Output Format section (fix A)
2. Add fallback precondition evidence codes to degraded-estimate block (fix D)
3. Revise Verification section to require a mandatory per-stop checklist artifact (fix B)
4. Add precondition language: "You may use fallback only AFTER documented attempts at all sites in scope"

**Stage 6 (travel-itinerary-builder fixes):**
1. Add Photo-Source Ledger requirement to Step 5 or new Step 5.5 (fix A)
2. Add explicit precondition gate before Step 6 (fix C)
3. Convert Step 7 verification prose into a mandatory pre-delivery verification table (fix B)
4. Hard-block rule: "Do not deliver if any stop has neither photo nor placeholder"

---

## Verification Gates (Post-Fix Re-audit)

After blue-team fixes are applied, re-verify:

1. **lodging-search:** Can an agent use WebSearch without at least producing a Tool-Use Ledger line? (Should be: NO, structure prevents it)
2. **lodging-search:** Can an agent claim "CAPTCHA on all sites" without listing which sites were attempted? (Should be: NO, reason code requires evidence)
3. **travel-itinerary-builder:** Can an agent skip Step 5 and proceed to Step 6? (Should be: NO, precondition gate blocks it)
4. **travel-itinerary-builder:** Can a stop ship without an image and without a placeholder? (Should be: NO, hard-block prevents it)
5. **Both skills:** Can an agent skip the pre-delivery verification table? (Should be: NO, both skills now require it as a mandatory artifact)

---

## Summary

All 9 CRITICAL gaps can be closed by adding structural enforcement (mandatory artifacts + explicit gates + evidence codes) on top of the existing prose policy. No complete rewrites needed; all fixes are additive and preserve the skills' intent and tone.

**Next stage:** Blue-team applies these fixes to both SKILL.md files and re-verifies against the criteria above.
