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

## Prerequisite: Search/Verification Tool Requirement

The practical logistics enrichment required in Step 2 below (departure times, parking, named venues, opening hours) depends on actually being able to verify facts, not just narrate plausibly. Before starting Step 2, confirm that `WebSearch` (or an equivalent live-web tool) is available this session.

If no search tool is available, do not silently degrade field-by-field as you go — say so up front, once: "I don't have live web access this session, so parking, venue names, and opening hours below can't be verified — I'll label every one of them as unconfirmed rather than stating a plausible guess as fact." Then apply that label consistently across every logistics field in the draft, the same way a stop with no lodging-search result gets an explicit degraded-estimate label rather than a silent gap. This mirrors `lodging-search`'s own tool-prerequisite check — the same verify-or-label-honestly standard applies to logistics facts as to lodging prices.

## Workflow

1. **Lock the traveler profile** (Step 1 above). Write the agreed profile — including the confirmed output language — into the itinerary draft as a compact summary block. Do not proceed until this is explicit.

2. **Produce or update a structured itinerary draft, in the confirmed language.** Include route overview, day-by-day legs, driving distances/times, lodging bases, activity blocks, links, a lodging budget table, and — critically — **preserve or add narrative texture**: nicknames, historical detail, sensory description, and the "why this works" reasoning behind route/lodging choices. A draft that reads like a spreadsheet will produce an HTML page that reads like one; narrative quality has to exist before the HTML step, not be invented during it.

   **Practical logistics enrichment (mandatory, not optional polish).** A route overview and a lodging table are not enough on their own — an itinerary that doesn't tell someone when to leave or where to park hasn't actually done the planning work. For every leg and every stop, include:
   - **Recommended departure time**, not just a travel duration — worked backward from the destination's opening hours, likely traffic, or (for any leg flagged as a driving-comfort or safety concern) the time of day that matters. Where specific traffic conditions aren't verifiable, state a qualitative default instead of inventing a precise-sounding number ("assume normal daytime traffic; add extra margin near a major city, on a holiday weekend, or in peak season"). A duration alone ("about 3 hours") without a "leave by" recommendation just shifts the planning work back onto the traveler.
   - **Travel time with a realistic buffer** for photo stops, traffic, and border/toll delays — state the buffer separately from the base estimate so it doesn't read as false precision.
   - **Parking**: name and location of where to leave the car at each stop, with a link when one is findable (official municipal parking page, a map link, or a review-site listing), approximate cost, and distance to the town center. If nothing suitable is found, say so explicitly ("no specific parking search done — confirm with your hotel on arrival") rather than omitting the topic entirely.
   - **Named venue recommendations, not generic categories.** "Dinner" is not a recommendation; a specific restaurant (or two, so the traveler has a choice) is. Same for any museum, landmark, or attraction mentioned — name it and link its official site or ticket page when found.
   - **Opening hours and closure days** for every specifically named restaurant, museum, or attraction — verified per-venue via search, never assumed or invented (Monday closures for museums and Sunday/Monday closures for restaurants are common traps worth actively checking, not guessing about).
   - **Reservation requirements**, where they matter: timed-entry museum tickets, restaurants that need advance booking in peak season, permit-gated activities.
   - **Time zone changes, payment norms, and connectivity notes** where genuinely relevant to the route (crossing a time zone or border, a region where small venues are cash-only, an area with unreliable mobile signal) — skip this bullet entirely for routes where none of it applies rather than padding every itinerary with boilerplate.

   Apply the same honesty rule used for lodging prices throughout: **verify, don't invent.** If a search doesn't turn up reliable opening hours, a parking option, or a specific venue, label the gap plainly ("hours not confirmed — verify locally") instead of stating a plausible-sounding guess as fact.

3. **Run adversarial refinement on the draft.** Check for profile-fit issues, routing problems, expectation mismatches, date/location consistency, lodging alignment, payment-method compatibility, translation accuracy (if the output language differs from the source material's language, spot-check that place names, opening hours, and factual claims survived translation correctly), and safety-relevant omissions (e.g. if an activity legally requires a permit or license, that requirement must be stated in the draft, not just implied by the activity name). **This review must explicitly include a privacy/PII lens (what personal detail is being collected and why, and whether it needs a visible warning before the file is shared), a link-safety lens (every domain referenced, including in plain body text, checked against the allowed-domains list), and a logistics-completeness lens (every leg has a departure time and buffer, every stop has a parking note, every named venue has hours or an explicit "not confirmed" label)** — a generic pass that only checks routing and dates has previously missed exactly these categories. If major issues are found, fix the draft and rerun the review. Do not build the HTML from a draft with unresolved major issues. Only skip this step with explicit user approval.

4. **Get lodging options via the `lodging-search` skill.** Its Step 0 (site scope confirmation) should already be resolved from the traveler profile's payment-method constraint — pass that through rather than re-asking. For each overnight stop, invoke it with location, dates, guests, rooms, and payment constraints. It returns up to 3 positioned options with pros/cons per stop, or a clearly labeled degraded estimate if live search failed — never a bare placeholder. **Before delivering the final HTML, verify every stop has either live options or a degraded estimate attached; a stop with neither is a hard blocker, not something to silently paper over with `[PENDING]`.** If, after `lodging-search`'s own retry logic, a stop still has nothing, surface this explicitly to the user rather than shipping a document with an unmarked gap.

5. **Source images for key locations.** For each major stop or highlight (not necessarily every single activity), find an openly-licensed photo (e.g. Wikimedia Commons) with clear attribution. Verify the license and author on the image's own page before using it — don't take a search snippet's word for the license. If no suitable licensed image is found for a location within a reasonable search effort, leave a labeled placeholder rather than using an unlicensed or unattributed image — a missing photo is honest; an uncredited one is a real risk. Record the source and license/attribution text alongside each image for the credits section. **Keep photo credits (license name, photographer's name, source) in their original form — do not translate license names or attribution text into the output language; these are legal/proper-noun terms and mistranslating them can misrepresent the license.**

6. **Build the static HTML itinerary page, in the confirmed language.** This is the step most likely to silently discard everything built in steps 2-5 if not done carefully — actively check against that failure mode. The page must:
   - **Preserve narrative content, not just links.** Port descriptive sentences (not compressed fragments), and include a condensed "Trip Overview / Why This Works" section carrying forward the route/lodging strategy reasoning from the draft, plus any safety-relevant modules (permits, licenses, tolls/vignettes) in full — these are functional content, not decoration, and must not be dropped.
   - **Default to a white/light background suitable for printing** — this is the default posture, not a special mode. If a dark on-screen theme is specifically requested, add a `@media print` override that switches to white background / dark text, hides non-printing elements (hover-only UI), and uses print-safe type sizing (≥12pt body text for anything meant to be read on paper).
   - **Give every day of a multi-night stay the same structural elements** as the check-in day — a compact "Staying at: [location]" line and a map link, even if the full priced lodging card only appears once. A reader jumping to any single day should never have to scroll back to find out where they're sleeping.
   - **Render the practical logistics enrichment from step 2** — departure time, travel-time buffer, and parking note for each leg; named venues with links and opening hours (or an explicit "not confirmed" label) for each stop — as visible, functional content, not a footnote. This is the material a traveler actually references while on the road, so it belongs in the flow of each day, not buried at the end of the page.
   - **Use map links, not embedded iframes requiring an API key.** Use an OpenStreetMap search link (`https://www.openstreetmap.org/search?query=<place>`) or a dedicated maps skill if one is available, and present it as a clickable link. Do not use a Google Maps Embed iframe with a placeholder API key — an unset key silently fails to render. A plain hyperlink to a Google Maps search (not an embed) is fine to keep if it already exists in source material.
   - **Embed the sourced images** from step 5 with visible attribution text near each image, not just in a buried credits footnote.
   - **Present lodging as up to 3 comparison cards per stop**, each showing the pro/con framing from `lodging-search`'s output — never collapse this to a single "selected" property unless the user has actually chosen one.
   - **Include a data freshness note** stating the generation date and that lodging prices are "as of [date]; verify before booking" — since OTA prices are dynamic and can go stale between generation and travel.
   - **Escape/sanitize all user-provided and draft-sourced text** before embedding it in HTML, to prevent rendering issues from special characters.

7. **Verify and deliver.**
   - Confirm every day has consistent structure (no day silently missing lodging/map sections it should have).
   - Confirm every stop has either live lodging options or an explicitly labeled degraded estimate — zero bare placeholders.
   - Confirm every narrative section from the source draft (strategy rationale, safety/permit info, practical logistics) survived into the HTML in some form, in the confirmed output language.
   - Confirm every leg has a departure time and travel-time buffer, every stop has a parking note (or an explicit "not searched" label), and every named venue has opening hours or an explicit "not confirmed" label — a logistics field silently dropped between draft and HTML is the same class of failure as a dropped narrative section.
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
- Venues: the venue's own official site or ticket/reservation page when one exists; a well-known reservation or ticketing platform (e.g. a restaurant-reservation platform, a museum's official ticketing partner) when it doesn't — found via search and verified before inclusion. A generic "best restaurants in X" listicle or aggregator article is not a substitute for either; if that's genuinely the best source found, label it plainly as a secondary/aggregator source in the text rather than presenting it as the venue's own page.
- Parking: official municipal parking-authority pages, or a map/review listing found via search and verified before inclusion — same verify-don't-invent rule as venues and opening hours

Check body text for domain mentions, not just hyperlinks — a plain-text reference to a non-whitelisted site (e.g. "try Airbnb or Booking.com") is still a domain reference and should either be added to the whitelist explicitly (if sanctioned) or removed.

## PII and output handling

- Collect only the fields necessary for itinerary generation, lodging search, and HTML rendering — avoid gathering more traveler detail than the output actually needs.
- Since the final HTML is designed to be shareable and printable, **include a visible note if it contains personal details** (home departure address, traveler count, payment preferences) so the user can decide whether to redact anything before sharing it further.
- Document the output file's location and note that it isn't automatically deleted — if the content is sensitive, the user may want to move or delete it after the trip.

## Notes

- Sequential dependency: interactive profile intake (incl. language) → draft (narrative-rich, in target language, with practical logistics enrichment — departure times, parking, named venues, opening hours) → adversarial refinement (incl. translation spot-check and logistics-completeness check) → lodging search (up to 3 options/stop, never blank) → image sourcing (licensed + attributed, untranslated credits) → HTML build (narrative + logistics + print-friendly by default + link-based maps) → verify → deliver.
- `adversarial-refinement` is a required gate before HTML generation, and its scope must include narrative-fidelity, translation-accuracy, and safety-relevant-omission checks, not just routing/date consistency.
- `lodging-search` is used only for lodging discovery, not booking, and always returns either live options or a labeled estimate — this skill should treat a lodging-search response with neither as a hard error, not something to paper over.
- If the user already has a complete, trusted itinerary and only wants the HTML page, skip adversarial refinement only with explicit user approval.
- If the user asks for the same itinerary in a second language after delivery, treat it as a translation pass over the already-verified content and photo credits, not a full rebuild — re-run only the translation-accuracy check, not the entire workflow.
