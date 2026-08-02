---
name: lodging-search
description: Find and compare up to 3 lodging options per stop on a multi-stop trip, using live browser search on a booking-site scope the user confirms, with a structured, never-blank fallback. Trigger when an itinerary needs lodging candidates, a specific stop needs hotel/apartment options, a prior lodging search returned no usable results, or the user needs to compare accommodation across a region with particular payment or site constraints.
---

# Lodging Search

Browser-based lodging discovery for itinerary planning. This skill does not book; it searches an explicitly confirmed set of booking sites and returns up to 3 structured, comparable candidates per stop — each with an explicit pros/cons framing — or a clearly labeled degraded estimate if live search is genuinely unavailable. It never returns a bare placeholder with nothing attached.

## When to Use

- Itinerary planning needs lodging options for one or more stops
- A user wants hotel/apartment alternatives near a town, lake, airport, or attraction
- A prior lodging search attempt failed or returned incomplete results
- User explicitly requests booking-site search or accommodation comparison

## Step 0: Confirm site scope (interactive, do this before anything else)

Never silently assume a regional booking-site list. Ask the user, or use what they've already told you in this conversation, to pick one:

1. **Global default** — mainstream international sites (booking.com, expedia.com, hotels.com, airbnb.com, agoda.com). Use this unless the user says otherwise.
2. **A named regional/payment preset** — see `references/site-presets.md` for ready-made lists (e.g. a Russian-OTA preset for MIR-card travelers, a US preset, a Europe preset). Ask which preset fits if the user has mentioned a payment-method constraint, a home country, or a preference for local booking sites.
3. **A fully custom list** — the user names specific sites directly ("only search Airbnb and Booking.com," "add hotels.ru to the list"). Use exactly what they specify; don't silently add or drop sites.

State the confirmed scope back in one line before searching (e.g. "Searching booking.com, expedia.com, and airbnb.com for each stop — let me know if you'd rather use a different set.") so the user can correct it before any browsing starts. If the user's payment method, currency, or stated home region make a preset an obvious fit, propose it — don't just wait silently.

## Payment-method realism

If the user has a specific payment-method constraint (a particular card network, cash-only, points redemption, a currency restriction, etc.), verify whether each candidate listing's shown payment method and timing (prepay vs. pay-on-arrival) actually fits it before recommending the candidate. If a listing's payment terms don't match a stated constraint, say so explicitly rather than silently ranking the property as if it matched.

## Prerequisite: Tool Requirement

This skill requires a live browser-control tool — either `mcp__claude-in-chrome__*` (navigate, read_page/get_page_text, find, computer for clicks) or `mcp__computer-use__*`. **Before starting any search, probe that the tool is actually available and connected** (e.g. `list_connected_browsers`, or a trivial navigation to a known page). If no browser tool is connected, stop immediately with one clear message: "Live browser access isn't connected — I can't search live listings. I can either (a) request access now, or (b) proceed with a degraded/estimated lodging block per stop instead." Do not enter the per-stop search loop without this check — a missing tool binding, not site-specific blocking, is the single most likely cause of a total-failure run.

**Before browser-based search, check whether a travel-booking MCP connector is already available** (e.g. a Booking.com or Expedia connector). If connected, prefer it over browser automation — it returns structured data directly and doesn't suffer from CAPTCHA/bot-detection fragility. If not connected, suggest to the user (once, not on every run) that connecting one would make future searches more reliable, then proceed with browser automation.

## Prerequisites (inputs)

- Confirmed site scope (Step 0)
- location, check-in and check-out dates, guests/rooms, and any special requirements — if any required input is missing, stop and request it before proceeding
- payment-method constraints, if specified
- budget band, if specified

## Workflow

1. **Site scope + tool + connector check** (see Step 0 and Prerequisite above) — do this once per session, not once per stop.

2. **Confirm required inputs** for this specific stop: location, dates, guests/rooms, special requirements, exclusions, payment constraints. If a stated payment-method constraint isn't a recognized method (e.g. it's garbled or ambiguous), surface it back to the user for confirmation rather than silently passing it through or ignoring it.

3. **Search strategy** — branch explicitly by what the page actually shows, rather than one undifferentiated "blocked" outcome:
   - **Consent/cookie dialog detected** → attempt one scripted dismissal, then re-check the page. This is not a blocker.
      - **Page still loading / results panel empty on first read** → poll with a short wait, up to 3 checks, before concluding "no results." Many OTA result panels are JS-rendered and need time.
         - **CAPTCHA or explicit bot-detection banner** → stop on this domain immediately, no retry on it, move to the next site in scope. Log which domain and which stop hit this.
            - **Fully loaded page, genuinely zero listings** → this is real empty inventory, not a blocker — report it as such (don't retry, don't treat it as a failure).
               - **Simplify-and-retry, if the first pass returns too few results**: try, in this fixed order, (a) widen the date window by ±1 day, (b) drop secondary filters (room-type/brand), (c) drop star-rating filter, (d) widen to a nearby town/city if the exact destination has thin inventory. Try at most 2 of these per stop before moving on.

               4. **Pacing** — wait a short interval (a few seconds) between consecutive navigations to the same domain, and between different domains in the same run. If a domain already returned a CAPTCHA/bot-detection block earlier in this session, don't re-hit it for a later stop — skip straight to the next site in scope for that stop.

               5. **Extract structured results.** For each visible property, capture: property name, rating/review score if shown, room type if shown, price total and currency, booking URL, cancellation/payment note, explicit payment-method compatibility (or "unverified" if not shown — never infer it). If a field is missing, mark it missing/N/A; never fabricate data.

               6. **Rank and select up to 3 candidates** for this stop — never more, never a long list. Prefer results with a clear total price, dates matching input, a bookable link, and visible payment/cancellation terms. Present them as **positioned options with explicit tradeoffs**, not a ranked top-N list: e.g. "best value," "best location," "most space," rather than #1/#2/#3 by a single score. Every option needs both a pro and a con — an option with no stated con reads as unvetted, not genuinely best.

               7. **If live search genuinely fails for this stop** (tool unavailable, or every site in scope hits CAPTCHA/dead-page after the retry steps above) — **never return a bare blocker with nothing attached.** Instead return a **degraded estimate block**: typical property type for the requested budget band and area, an approximate nightly price range (explicitly labeled "estimated, not live-sourced — verify before booking"), and a one-line manual-verification pointer (e.g. "search booking.com manually for [location] on [dates]"). This is what prevents an itinerary from ever showing a bare `[PENDING]`.

               ## Output Format

               Use this structure per property (live result):

               ```md
               ### [Property Name] — [one-line positioning, e.g. "Best value" / "Best lake view" / "Most space"]
               - Site: [domain]
               - Rating: [X / 10, or "not shown"]
               - Room: [type]
               - Total: [amount + currency] for [dates]
               - Payment: [prepayment / pay upon arrival / unverified] — [flag mismatch with traveler's stated preference if present]
               - Cancellation: [note, or "not shown"]
               - Link: [URL]
               - Pro: [specific, concrete reason to pick this one]
               - Con: [specific, concrete tradeoff — never omit this]
               ```

               Use this structure when live search failed for a stop (degraded estimate — never a bare blocker):

               ```md
               ### [Location] — Estimated only (live search unavailable)
               - Typical property type for this budget band: [e.g. "1-2BR self-catering apartment"]
               - Estimated nightly range: [range] — **not live-sourced; verify before booking**
               - Reason live search failed: [tool unavailable / CAPTCHA on all sites in scope / genuinely zero inventory found]
               - Manual verification: [specific site + search terms to try]
               ```

               Provide up to 3 candidates per stop (fewer only if genuinely fewer exist with usable data).

               ## Constraints

               - Do not complete checkout or reservation flows, ever.
               - Do not enter personal traveler details into any site.
               - Do not bypass CAPTCHA or login walls — treat either as a stop signal for that domain, not an obstacle to route around.
               - Only search sites within the confirmed scope (Step 0) — do not add or substitute sites on your own judgment.
               - If a site blocks automated interaction, log it and move to the next site in scope — never retry the same block indefinitely.
               - If pricing is missing or dynamic, say so; never invent a price.
               - All prices carry an implicit "as of [today's date]" — note this explicitly in the itinerary that consumes this output, since OTA prices change.

               ## Verification

               After search completes for each stop, verify: URLs correspond to the selected site's actual domain; dates in results match requested dates; the stop has either ≥1 live result or a degraded estimate block — never neither.

               ## Notes

               - This skill is intentionally narrow: search only, no booking, no account creation, no payment.
               - For multi-stop itineraries, run one search per stop, respecting the pacing rule above — do not fire all stops' searches back-to-back with no delay.
               - If a travel-booking MCP connector is available, prefer it for the "extract structured results" step; browser automation remains the fallback for sites in scope without a connector.
               - **For multi-stop runs, report status after each stop** (e.g. "Stop 2 of 5: [location] — 3 live options found"; "Stop 3 of 5: [location] — CAPTCHA on 2 sites, degraded estimate returned"), rather than only surfacing results as one wall of output at the very end. This lets the calling skill (and the user) catch a systemic failure at stop 1-2 instead of discovering it only after all stops have run.
               - See `references/site-presets.md` for the ready-made regional/payment presets and how to add your own.
               
