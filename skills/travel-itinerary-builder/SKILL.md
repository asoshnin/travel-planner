---
name: travel-itinerary-builder
description: Interactively collect a traveler profile, then build a reviewed, narrative-rich, print-friendly static HTML trip itinerary — in the traveler's chosen language — with embedded attributed photos, up to 3 compared lodging options per stop, and a white background suitable for printing. Trigger when the user wants to build, improve, or finalize a trip itinerary, or turn a draft into a polished shareable/printable page.
---

# Travel Itinerary Builder

Turns a rough or narrative-rich trip draft into a reviewed, day-by-day itinerary and a static HTML travel page that reads as a genuinely engaging trip document — not a stripped link index — with embedded photos, real lodging comparisons, and a printable white-background layout, in whatever language the traveler wants to read it in.

## When to Use

- User asks to build, improve, or finalize a trip itinerary
- User provides a draft (or rough notes) and wants it turned into a polished, printable page
- User wants a shareable static HTML trip page with real lodging options and photos
- User explicitly requests itinerary optimization, narrative polish, print-friendly formatting, or a translated version of an existing itinerary

## Step 1: Interactive traveler-profile intake (mandatory, do not skip)

This is the step most likely to be rushed — don't rush it. Even when a draft already answers most of these, **read back a compact summary and get explicit confirmation before moving on.** Silently extracting values from a draft and proceeding is not the same as confirming them, and profile mistakes here (wrong dates, wrong budget, wrong language) propagate into every later step.

Collect or confirm, interactively:

- trip dates and overall duration
- departure point and final return point
- travelers and any special needs
- pacing preferences: relaxed vs packed, scenic detours vs fastest route
- lodging style and budget band
- activity priorities: beaches, hikes, sights, food, etc.
- must-have constraints: tolls/vignettes, permits/licenses required for any activity, border stops, time windows
- payment-method constraints, if any (hand this to `lodging-search`'s Step 0 site-scope confirmation)
- **output language** — ask explicitly if not stated: what language should the itinerary be written in? Default to the language the user is writing to you in, but confirm rather than assume, especially if the trip involves a country whose language differs from the user's own (a traveler may want the itinerary in a companion's language, or in English regardless of their own).
- **whether the output needs to be printable** (default: yes, treat every itinerary as potentially printed unless told otherwise — this determines the color palette from the start rather than as an afterthought)

If any required input is missing, ask for it before continuing. Use a short structured exchange (a handful of direct questions, or a numbered confirmation list) rather than one giant open-ended question — it's easier for the traveler to correct one wrong field in a list than to redraft a paragraph.

## Workflow

1. **Lock the traveler profile** (Step 1 above). Write the agreed profile — including the confirmed output language — into the itinerary draft as a compact summary block. Do not proceed until this is explicit.

2. **Produce or update a structured itinerary draft, in the confirmed language.** Include route overview, day-by-day legs, driving distances/times, lodging bases, activity blocks, links, a lodging budget table, and — critically — **preserve or add narrative texture**: nicknames, historical detail, sensory description, and the "why this works" reasoning behind route/lodging choices. A draft that reads like a spreadsheet will produce an HTML page that reads like one; narrative quality has to exist before the HTML step, not be invented during it.

3. **Run adversarial refinement on the draft.** Check for profile-fit issues, routing problems, expectation mismatches, date/location consistency, lodging alignment, payment-method compatibility, translation accuracy (if the output language differs from the source material's language, spot-check that place names, opening hours, and factual claims survived translation correctly), and safety-relevant omissions (e.g. if an activity legally requires a permit or license, that requirement must be stated in the draft, not just implied by the activity name). **This review must explicitly include a privacy/PII lens (what personal detail is being collected and why, and whether it needs a visible warning before the file is shared) and a link-safety lens (every domain referenced, including in plain body text, checked against the allowed-domains list) — a generic pass that only checks routing and dates has previously missed exactly these two categories.** If major issues are found, fix the draft and rerun the review. Do not build the HTML from a draft with unresolved major issues. Only skip this step with explicit user approval.

4. **Get lodging options via the `lodging-search` skill.** Its Step 0 (site scope confirmation) should already be resolved from the traveler profile's payment-method constraint — pass that through rather than re-asking. For each overnight stop, invoke it with location, dates, guests, rooms, and payment constraints. It returns up to 3 positioned options with pros/cons per stop, or a clearly labeled degraded estimate if live search failed — never a bare placeholder. **Before delivering the final HTML, verify every stop has either live options or a degraded estimate attached; a stop with neither is a hard blocker, not something to silently paper over with `[PENDING]`.** If, after `lodging-search`'s own retry logic, a stop still has nothing, surface this explicitly to the user rather than shipping a document with an unmarked gap.

5. **Source images for key locations.** For each major stop or highlight (not necessarily every single activity), find an openly-licensed photo (e.g. Wikimedia Commons) with clear attribution. Verify the license and author on the image's own page before using it — don't take a search snippet's word for the license. If no suitable licensed image is found for a location within a reasonable search effort, leave a labeled placeholder rather than using an unlicensed or unattributed image — a missing photo is honest; an uncredited one is a real risk. Record the source and license/attribution text alongside each image for the credits section. **Keep photo credits (license name, photographer's name, source) in their original form — do not translate license names or attribution text into the output language; these are legal/proper-noun terms and mistranslating them can misrepresent the license.**

6. **Build the static HTML itinerary page, in the confirmed language.** This is the step most likely to silently discard everything built in steps 2-5 if not done carefully — actively check against that failure mode. The page must:
   - **Preserve narrative content, not just links.** Port descriptive sentences (not compressed fragments), and include a condensed "Trip Overview / Why This Works" section carrying forward the route/lodging strategy reasoning from the draft, plus any safety-relevant modules (permits, licenses, tolls/vignettes) in full — these are functional content, not decoration, and must not be dropped.
      - **Default to a white/light background suitable for printing** — this is the default posture, not a special mode. If a dark on-screen theme is specifically requested, add a `@media print` override that switches to white background / dark text, hides non-printing elements (hover-only UI), and uses print-safe type sizing (≥12pt body text for anything meant to be read on paper).
         - **Give every day of a multi-night stay the same structural elements** as the check-in day — a compact "Staying at: [location]" line and a map link, even if the full priced lodging card only appears once. A reader jumping to any single day should never have to scroll back to find out where they're sleeping.
            - **Use map links, not embedded iframes requiring an API key.** Use an OpenStreetMap search link (`https://www.openstreetmap.org/search?query=<place>`) or a dedicated maps skill if one is available, and present it as a clickable link. Do not use a Google Maps Embed iframe with a placeholder API key — an unset key silently fails to render. A plain hyperlink to a Google Maps search (not an embed) is fine to keep if it already exists in source material.
               - **Embed the sourced images** from step 5 with visible attribution text near each image, not just in a buried credits footnote.
                  - **Present lodging as up to 3 comparison cards per stop**, each showing the pro/con framing from `lodging-search`'s output — never collapse this to a single "selected" property unless the user has actually chosen one.
                     - **Include a data freshness note** stating the generation date and that lodging prices are "as of [date]; verify before booking" — since OTA prices are dynamic and can go stale between generation and travel.
                        - **Escape/sanitize all user-provided and draft-sourced text** before embedding it in HTML, to prevent rendering issues from special characters.

                        7. **Verify and deliver.**
                           - Confirm every day has consistent structure (no day silently missing lodging/map sections it should have).
                              - Confirm every stop has either live lodging options or an explicitly labeled degraded estimate — zero bare placeholders.
                                 - Confirm every narrative section from the source draft (strategy rationale, safety/permit info, practical logistics) survived into the HTML in some form, in the confirmed output language.
                                    - Confirm the page renders readably in print preview (white background, dark text, no reliance on background color alone for section separation).
                                       - Confirm all links correspond to approved domains (see below) and that any domain mentioned in body text — not just `<a href>` targets — is also checked, not only hyperlinked ones.
                                          - Confirm photo attribution text is intact and untranslated where required.
                                             - Deliver the final files: refined itinerary draft, static HTML page.

                                             ## Constraints

                                             - Do not book anything or complete checkout flows (that's `lodging-search`'s constraint too — this skill never touches booking flows directly).
                                             - Do not enter personal traveler details anywhere beyond what's needed for the itinerary itself.
                                             - Do not bypass CAPTCHA or login walls.
                                             - Do not invent prices, availability, payment conditions, or factual claims (e.g. license fees, regulations) that weren't in the source draft or a verified search — if uncertain, say so explicitly in the output rather than stating it as fact.
                                             - Do not silently machine-translate factual details (opening hours, prices, addresses) without flagging that translation occurred — a wrong address in a beautifully translated sentence is still a wrong address.
                                             - Keep the HTML usable on both mobile and print; these are not competing goals if the stylesheet is built with both from the start.

                                             ## Allowed domains

                                             - Maps: openstreetmap.org, google.com/maps (as link-only, never embed)
                                             - Booking: whatever `lodging-search` returns (its own confirmed site scope governs this — don't add domains here independently)
                                             - Images: commons.wikimedia.org, or other sources with a clear open license — always with attribution
                                             - Activities: links explicitly present in the user's draft, or whitelisted by the user

                                             Check body text for domain mentions, not just hyperlinks — a plain-text reference to a non-whitelisted site (e.g. "try Airbnb or Booking.com") is still a domain reference and should either be added to the whitelist explicitly (if sanctioned) or removed.

                                             ## PII and output handling

                                             - Collect only the fields necessary for itinerary generation, lodging search, and HTML rendering — avoid gathering more traveler detail than the output actually needs.
                                             - Since the final HTML is designed to be shareable and printable, **include a visible note if it contains personal details** (home departure address, traveler count, payment preferences) so the user can decide whether to redact anything before sharing it further.
                                             - Document the output file's location and note that it isn't automatically deleted — if the content is sensitive, the user may want to move or delete it after the trip.

                                             ## Notes

                                             - Sequential dependency: interactive profile intake (incl. language) → draft (narrative-rich, in target language) → adversarial refinement (incl. translation spot-check) → lodging search (up to 3 options/stop, never blank) → image sourcing (licensed + attributed, untranslated credits) → HTML build (narrative + print-friendly by default + link-based maps) → verify → deliver.
                                             - `adversarial-refinement` is a required gate before HTML generation, and its scope must include narrative-fidelity, translation-accuracy, and safety-relevant-omission checks, not just routing/date consistency.
                                             - `lodging-search` is used only for lodging discovery, not booking, and always returns either live options or a labeled estimate — this skill should treat a lodging-search response with neither as a hard error, not something to paper over.
                                             - If the user already has a complete, trusted itinerary and only wants the HTML page, skip adversarial refinement only with explicit user approval.
                                             - If the user asks for the same itinerary in a second language after delivery, treat it as a translation pass over the already-verified content and photo credits, not a full rebuild — re-run only the translation-accuracy check, not the entire workflow.
                                             
