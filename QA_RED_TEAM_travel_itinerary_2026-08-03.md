# QA Red-Team Audit: Travel Itinerary Builder
## Photo-Sourcing Enforcement Gap Analysis

**Date:** 2026-08-03  
**File Audited:** `travel-itinerary-builder/SKILL.md`  
**Audit Focus:** Structural enforcement of photo-sourcing workflow and placeholder verification

---

## Executive Summary

The travel-itinerary-builder SKILL.md contains **stated policy** for photo sourcing and placeholder management (Step 5, Step 7), but **lacks structural enforcement** that would prevent an agent from silently omitting photos and placeholders. An agent could skip Step 5's photo-sourcing entirely and proceed directly to Step 6 (HTML build) with no stops having images or placeholders, and Step 7's verification checklist would not catch this omission because it is prose guidance, not a mandatory artifact.

**Severity:** **CRITICAL** — Photos are central to itinerary quality and trust; silent omission with no placeholder is indistinguishable (to a user) from "the agent tried but there was nothing available." The skill's current wording does not guarantee that a gap between intent and output will be surfaced.

**Findings:** 3 CRITICAL enforcement gaps identified.

---

## Finding 1: No Explicit Precondition Gate Before HTML Build

**Severity:** CRITICAL

**Locus:** Step 5 → Step 6 transition (lines 61–65)

**Issue:** Step 5 instructs photo sourcing with a fallback to placeholders:
> "If no suitable licensed image is found for a location within a reasonable search effort, **leave a labeled placeholder** rather than using an unlicensed or unattributed image"

However, the workflow does not require explicit confirmation that Step 5 is complete before proceeding to Step 6. The sequential dependency (line 114) lists "image sourcing (licensed + attributed, untranslated credits)" as a distinct step, but the text contains **no precondition** stating: "Do not proceed to Step 6 until every major stop has either [1] a licensed image with recorded attribution, or [2] a visible placeholder marking the failed search."

**How This Gap Can Be Exploited:**
An agent could:
1. Skip Step 5 entirely (or read it and decide "I don't need photos for this itinerary").
2. Proceed directly to Step 6 (HTML build) with the draft.
3. In Step 6, render the draft-as-HTML with no images and no placeholders for any stop.
4. In Step 7, skim the prose checklist ("Confirm every stop has... [photo attribution text is intact]") and claim completion without producing a mandatory artifact (e.g., a ledger) that would prove the check happened.
5. Deliver the HTML with zero images and zero placeholders, undetected.

The skill text does not prevent this path.

**Evidence from SKILL.md:**
- Line 61–63: Step 5 says *what to do* but not *when you're done or what must happen next*.
- Lines 76–84 (Step 7): The verification section uses prose conditional language ("Confirm every stop has...") which an agent can mentally execute without producing a verifiable artifact.
- Line 115: The note says "image sourcing (licensed + attributed, untranslated credits)" is a step, but there is no explicit hard gate: "Do not proceed past this step until..."

---

## Finding 2: No Distinction Between "Searched and Found No Licensed Photo" vs. "Did Not Search"

**Severity:** CRITICAL

**Locus:** Step 5 instruction (lines 61–63)

**Issue:** The step says to find images or leave a placeholder if the search fails:
> "If no suitable licensed image is found for a location within a reasonable search effort, leave a labeled placeholder..."

This creates two possible end states:
- Image found → include with attribution
- Search performed, no image found → include placeholder

But the instruction does not define **what happens if the agent never performs the search**. The output (HTML + final itinerary) will look identical whether the agent: (a) searched thoroughly and found nothing, or (b) didn't search at all and omitted the step.

**How This Gap Can Be Exploited:**
An agent could:
1. Read Step 5 but decide photo sourcing is optional or too time-consuming.
2. Skip the search step entirely (internal state: no record, no ledger).
3. Move to Step 6 with no images in the draft.
4. Build HTML with no images.
5. In the final output, a stop with no image and no `[NO PHOTO FOUND]` placeholder looks like "we searched but had no luck" — when it actually means "we didn't search."
6. The user cannot tell the difference from the delivered artifact.

**Evidence from SKILL.md:**
- Line 61–63: The instruction assumes the search was performed ("If no suitable licensed image is found") but does not mandate it or require a record of the attempt.
- Lines 71 (Step 6): "Embed the sourced images from step 5 with visible attribution text" — implies images exist, but if Step 5 was skipped, there's no record of the skip.
- Line 83 (Step 7): "Confirm photo attribution text is intact and untranslated where required" — only checks if photos exist and are properly credited, not whether every stop *should* have a photo or placeholder.

---

## Finding 3: Step 7 Verification Checklist Is Prose Guidance, Not a Mandatory Checkable Artifact

**Severity:** CRITICAL

**Locus:** Step 7, lines 76–84

**Issue:** Step 7 uses imperative prose ("Confirm...") but does not require the agent to produce a verifiable artifact (e.g., a signed-off checklist, ledger, or summary table) that the user can review:

> "Confirm every day has consistent structure (no day silently missing lodging/map sections it should have).  
> Confirm every stop has either live lodging options or an explicitly labeled degraded estimate — zero bare placeholders.  
> Confirm... photo attribution text is intact..."

These read as instructions for the agent to think through, not deliverables the agent must produce and present. An agent can mentally check these boxes without writing anything down, making the verification process invisible to the user.

**How This Gap Can Be Exploited:**
An agent could:
1. Read Step 7's verification checklist.
2. Mentally skim it ("photos... yes, I'll make sure photos have attribution").
3. Produce no summary table, ledger, or checklist artifact.
4. Deliver the HTML.
5. If the user later asks "were all stops checked?", the agent has no record to show — only an oral claim of compliance.
6. More dangerously, if the agent skipped photos entirely, Step 7's mental checklist would be easy to skim ("did I include photo attribution for the photos I have?" — if there are zero photos, the answer is technically yes).

**Evidence from SKILL.md:**
- Line 77–80: Uses "Confirm..." (mental verification) rather than "Produce a checklist showing..." or "Document and deliver a verification table with columns: Stop | Photo [Yes/No] | Placeholder [Yes/No] | Source".
- Line 84: "Deliver the final files: refined itinerary draft, static HTML page." — no mention of a verification artifact as part of delivery.
- Compare to Step 4 (line 61): For lodging, the step explicitly says "verify every stop has either live options or a degraded estimate attached; a stop with neither is a hard blocker" and mentions `lodging-search` *returns* these as part of its output. Photo sourcing has no equivalent mandatory return artifact.

---

## Before/After Examples

### Current State (Gap Exploitation Scenario)

**Agent workflow (undetected omission):**

```
Step 1: Collect traveler profile ✓
Step 2: Build narrative draft ✓
Step 3: Run adversarial refinement ✓ (doesn't explicitly check photo sourcing)
Step 4: Get lodging via lodging-search ✓
Step 5: "Source images for key locations" — Agent reads this, decides "not critical for MVP", skips
Step 6: Build HTML
   - Port narrative, logistics, lodging cards ✓
   - Render images from step 5 → (nothing, since step 5 was skipped)
   - No images in HTML, no placeholders
Step 7: "Verify and deliver"
   - Agent mentally checks: "photos have attribution" → looks for photos → finds zero → implicitly passes ("no photos to verify")
   - Delivers HTML with zero images, zero placeholders
   - Delivers itinerary draft (also no image placeholders)

User receives: Itinerary with no photos, no markers indicating photos were searched for and not found.
Outcome: User assumes "the agent tried to find photos but couldn't" when actually "the agent skipped photo sourcing."
```

**What user sees in delivered HTML:**
- Day 1: "Visit Paris — See the Louvre Museum"
  - No image embedded
  - No `[NO LICENSED PHOTO FOUND — placeholder]` marker
  - No indication of whether a photo search was attempted

---

### Proposed Fix (Enforcement Structural Changes)

**Option A: Mandatory Photo-Sourcing Ledger (Least Disruptive)**

Insert after Step 5:

> **Step 5.5: Photo-Sourcing Verification**
> 
> Before proceeding to Step 6, produce a photo-sourcing ledger — one line per major stop:
> ```
> Stop Name | Photo Sourced? | Source (or "NO PHOTO FOUND") | Placeholder Added?
> ```
> Example:
> ```
> Louvre Museum | YES | Wikimedia Commons (user: [name]) | N/A
> Eiffel Tower | NO | Search returned only copyrighted images | YES — placeholder "[NO LICENSED PHOTO FOUND]" added to draft
> ```
> 
> Do not proceed to Step 6 until every major stop has a ledger entry showing either [Photo Sourced: YES] or [Placeholder Added: YES].

**Benefit:** Creates a checkable artifact. User can verify at a glance whether photo sourcing was attempted for every stop. Agent cannot claim completion without producing this ledger.

---

**Option B: Pre-Delivery Verification Table (Highest Enforcement)**

Revise Step 7:

> **Step 7: Verify and Deliver**
> 
> Before delivering, produce a pre-delivery verification table:
> 
> | Stop | Photo (Y/N) | Source | Placeholder if No Photo (Y/N) | Lodging (Y/N) | Narrative (Y/N) |
> |------|-----------|--------|------------------------------|--------------|-----------------|
> | [Stop Name] | Y | [source] | N/A | Y | Y |
> | [Stop Name] | N | [reason] | Y | Y | Y |
> 
> **Hard-block rules:**
> - A stop with Photo = "N" AND Placeholder = "N" is not permitted. The delivery does not proceed.
> - A stop with Lodging = "N" is not permitted (mirrors Step 4's rule).
> - A stop with Narrative = "N" is not permitted.
> 
> Once the table passes all hard-block checks, present it to the user as part of the delivery package (alongside the HTML and draft).

**Benefit:** Highest enforcement. Every stop is explicitly reviewed and signed off. Photo omission without a placeholder is a hard block, not an oversight.

---

**Option C: Explicit Precondition in Step 5 (Minimal Scope)**

Insert at the beginning of Step 6:

> **Precondition: Photo sourcing complete**
> 
> Do not begin Step 6 until:
> - Every major stop has either [1] a licensed image with recorded source/attribution, or [2] a visible placeholder in the draft marked `[NO LICENSED PHOTO FOUND]`.
> 
> If this condition is not met, return to Step 5 and complete photo sourcing for any missing stops. A stop with neither an image nor a placeholder is a hard blocker.

**Benefit:** Simplest addition. Makes the gate explicit without requiring new artifacts.

---

## Summary of Enforcement Gaps

| Gap | Severity | Current State | Impact |
|-----|----------|---------------|--------|
| No precondition gate before Step 6 | CRITICAL | Step 5 → Step 6 transition is unguarded | Agent can skip Step 5 and proceed to HTML build with no images and no placeholders |
| No distinction: searched vs. didn't search | CRITICAL | Output looks identical whether photo sourcing was attempted or skipped | User cannot verify effort; silent omission is undetectable |
| Step 7 verification is prose, not artifact | CRITICAL | Step 7 uses "Confirm..." (mental check) rather than "Produce and deliver..." | Agent can claim verification without showing work; checklist easy to skim if photos are missing |

---

## Conclusion

The skill's **intent** (stated policy in Steps 5 and 7) is sound: find photos, use placeholders if not found, verify before delivery. However, the **structure** (no explicit gates, no mandatory artifacts, no hard-block preconditions) allows an agent to silently omit photos and placeholders without detection.

A user delivering an itinerary with zero images and zero photo-search indicators would not be able to tell from the final HTML whether:
- The agent searched thoroughly and found no licensed images (expected, acceptable)
- The agent skipped photo sourcing entirely (a process failure)

**To close these gaps, add:**
1. An explicit precondition before Step 6: every stop must have a photo + source OR a visible placeholder
2. A mandatory artifact (ledger or verification table) the agent produces and includes in the delivery
3. A hard-block rule preventing steps 6 and 7 from proceeding if any stop has neither image nor placeholder

---

Red-team audit of travel-itinerary-builder complete. **3 CRITICAL** findings identified; **0 MAJOR**; **0 MINOR**.
