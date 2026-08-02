# Example output: "The Someday Italy Loop"

> **This is a fictional demo, not a real trip.** It's a sample output built to show what `travel-planner` produces, not the author's actual vacation (his actual budget currently tops out at a weekend camping trip, tent included). If this repo gets enough stars, maybe someday it'll fund the real thing — until then, treat every price below as illustrative, not a live quote.
>
> **The parking, restaurant, landmark, and opening-hours details below are real** — verified via web search while building this example, each with a source link — because that's what this skill is supposed to do on a real run. Only the lodging prices are illustrative placeholders (a real run replaces those with `lodging-search`'s live results); everything else here follows the same verify-don't-invent rule the skill now requires. Hours and prices can still change after this was written — confirm anything time-sensitive before you travel.

This is the kind of output you'd get by handing the assistant a one-line prompt like:

> "Plan a 6-day road trip landing in Pisa, hitting Cinque Terre, Tuscany, and the Amalfi Coast, flying out of Naples. Two adults, relaxed pace, boutique/small hotels, mid-range budget, English."

---

## Traveler profile (confirmed before drafting — see Step 1 of `travel-itinerary-builder`)

| Field | Value |
|---|---|
| Dates | 6 days / 5 nights (illustrative — no fixed calendar dates) |
| Travelers | 2 adults |
| Pace | Relaxed — 2 nights per stop, no single-night hotel-hopping |
| Lodging style / budget | Boutique or small independent hotels, mid-range |
| Priorities | Scenery, food, walkable historic centers, one full "do nothing" afternoon per stop |
| Constraints | Rental car with an automatic transmission (by request); driver not comfortable on very narrow cliff roads at night |
| Output language | English |
| Printable | Yes (this is the default) |

---

## Route overview

Pisa (fly in) → **Vernazza, Cinque Terre** (2 nights) → **San Gimignano, Tuscany** (2 nights) → **Positano, Amalfi Coast** (2 nights) → Naples (fly out)

**Why this route works:** it runs north to south in one direction, so there's no backtracking — each leg is a genuine step toward the airport you're leaving from. Cinque Terre first, while the legs are freshest, since its best moments (Vernazza's harbor, the cliffside trail sections that are open) reward walking, not driving. Tuscany in the middle absorbs the longest single drive as a full travel day with a wine-region lunch break built in, rather than tacking it onto either end. Amalfi goes last and closest to Naples airport, because that coast road is the one place on this trip worth being rested for, not rushed through.

Total driving across the trip: roughly 6.5–7 hours, split across two travel days — never more than about 4 hours in one sitting.

---

## Day 1–2 — Vernazza, Cinque Terre

**Getting here:** Land in Pisa, pick up the rental car, and aim to leave the airport by **10:30 AM** — Vernazza's own parking is small and fills up fast, so earlier is genuinely better here, not just a nice-to-have. Drive north (about 1 hr 15 to the La Spezia area; allow a 20–30 min buffer for airport-lot traffic and the last stretch of coastal road). None of the five Cinque Terre villages take cars past their outer lots.

**Parking:** [Vernassoa Parking](https://www.vernazzaparking.it/english/index.html) — guarded, with a shuttle to the village center running roughly 8:00 AM–8:00 PM, about €2.50/hour or ~€20/day. It's genuinely limited and fills up in summer; if it's full, the fallback is parking in Monterosso, Levanto, or La Spezia and taking the train in instead. *(Source: [Where to park in Vernazza](https://www.lecinqueterre.org/eng/faq/parcheggivernazza.php), [La Spezia Guide](https://laspeziaguide.com/insider-guide/everything-cinque-terre/getting-to-and-around-cinque-terre/driving-and-parking-in-cinque-terre/))*

Vernazza is the one with the harbor everyone photographs — a tight cluster of pastel buildings wrapped around a natural breakwater, with the ruins of a 14th-century watchtower over the water. Base here rather than in Monterosso (bigger, busier) or Riomaggiore (better nightlife, worse for an early one).

**Day 1:** Arrive, settle in, walk the harbor at golden hour. For dinner: **[Ristorante Belforte](https://www.eng.ristorantebelforte.it/)**, built into the old fortress on the cliff edge — widely considered the best table in Cinque Terre, known for seafood (the Spaghetti alla Bruno is the house favorite), roughly €35–60/head. **Closed Tuesdays, and reservations are genuinely necessary** — book ahead, not on arrival. *(Source: [Frommer's](https://www.frommers.com/destinations/the-cinque-terre/restaurants/belforte/), [Visit Vernazza](https://visitvernazza.org/en/food-drink/ristorante-belforte/))*

**Day 2:** The full "do nothing" day. Either the Sentiero Azzurro trail section toward Corniglia (check open/closed status locally — sections close for maintenance and after storm damage more often than people expect), or the short climb up to **[Doria Castle](https://en.milazzo.life/visitare-castello-doria-di-vernazza/)** — the watchtower ruins visible from the harbor. Summer hours (June–September) are roughly **11:00 AM–7:00 PM**, admission around €2.20, but hours shift with season and weather. **Confirm hours locally before making it your one plan for the day** — this one genuinely varies. Dinner in Vernazza again rather than train-hopping for one more town — this is the relaxed-pace request in action.

**Staying at:** Vernazza · [Map](https://www.openstreetmap.org/search?query=Vernazza%2C%20Italy)

### Lodging options — Vernazza (illustrative, not a live quote)

| | Option A | Option B | Option C |
|---|---|---|---|
| **Type** | Boutique harbor-view hotel | Family-run guesthouse | Restored village apartment |
| **Illustrative nightly range** | €220–260 | €120–150 | €160–190 |
| **Pros** | Harbor view from most rooms; on-site breakfast terrace | Genuinely local, half the price, walkable to everything | Kitchenette (useful — Vernazza's few restaurants fill up fast in season); most space for the money |
| **Cons** | Books out months ahead in peak season; steep steps, no elevator | No view, thin walls in the older wing | 10-minute uphill walk with luggage; no daily housekeeping |

*A real run of this skill searches your confirmed booking sites live for each stop and returns three actual, dated listings in this same format — this table is a stand-in to show the shape of that output.*

---

## Day 3–4 — San Gimignano, Tuscany

**Getting here:** Recommended departure from the La Spezia/Vernazza area: **10:30 AM**, to arrive San Gimignano by early afternoon with time to settle in before an evening walk. Drive south (about 2 hr 45 with a stop; allow a 20-min buffer for the drive through Val d'Elsa's smaller roads) into the Val d'Elsa. San Gimignano is nicknamed "the medieval Manhattan" for the 14 surviving stone towers (from an original 72) that noble families built centuries ago purely to outdo each other — a UNESCO World Heritage historic center that looks like a skyline from the 1300s.

**Parking:** **[Parcheggio Giubileo (P1)](https://www.comune.sangimignano.si.it/it/page/p1-giubileo)** — the official municipal lot, 305 spaces, a few hundred meters south of the historic center. Rates (per the comune's own site): €2.00/hour for the first two hours, €1.50/hour after, €7.00 for 24 hours. A shuttle bus (Line 2, April–October, ~€1 round trip) connects it to the town center if you'd rather not walk. *(Source: [Comune di San Gimignano](https://www.comune.sangimignano.si.it/it/page/p1-giubileo))*

**Day 3:** Arrive by early afternoon, walk the town before the day-trip coach crowds arrive at midday tomorrow (San Gimignano is a popular bus-tour stop — early morning and evening are noticeably quieter). Dinner: **[Le Vecchie Mura](https://www.vecchiemura.it/en/)**, built right into the old city walls with panoramic Val d'Elsa views — order the wild boar ragu or a T-bone, and a glass of Vernaccia di San Gimignano, the town's own DOCG white. **Dinner only, roughly 6:00–9:45 PM, Wednesday–Monday; closed Tuesdays.** *(Source: [official site](https://www.vecchiemura.it/en/contact/))* Book ahead in peak season.

**Day 4:** Climb **[Torre Grossa](https://www.san-gimignano-tickets.com/san-gimignano-civic-museums/)**, the tallest surviving tower and the only one open to visitors, plus the civic museum inside it. Hours: **9:00 AM–7:00 PM, April–September; 11:00 AM–5:00 PM, October–March** (last entry 30 minutes before closing) — €9 single ticket, or a €15 two-day pass covering all the civic museums. *(Source: official ticketing via [San Gimignano Civic Museums](https://www.san-gimignano-tickets.com/san-gimignano-civic-museums/))* Follow it with a half-day out into the countryside — a small vineyard visit near the town (many take walk-ins for a tasting, some require booking a day ahead) — then back for the "do nothing" half: a slow lunch, an afternoon in the shade of the Piazza della Cisterna.

**Staying at:** San Gimignano · [Map](https://www.openstreetmap.org/search?query=San%20Gimignano%2C%20Italy)

### Lodging options — San Gimignano (illustrative, not a live quote)

| | Option A | Option B | Option C |
|---|---|---|---|
| **Type** | Countryside agriturismo, 10 min drive out | In-town boutique hotel, tower view | Small in-town B&B |
| **Illustrative nightly range** | €180–210 | €200–240 | €110–140 |
| **Pros** | Pool, vineyard views, quiet at night | Walk everywhere, several rooms face the towers directly | Cheapest, warm hosts, good breakfast |
| **Cons** | Needs the car for every trip into town | Can hear the evening piazza crowd from lower floors | Small rooms, stairs only |

*Same note as above — illustrative, not a live search result.*

---

## Day 5–6 — Positano, Amalfi Coast

**Getting here:** This is the leg where the departure time is a safety decision, not a convenience one. Recommended departure from San Gimignano: **11:00 AM**, to arrive Positano by mid-afternoon — well before dusk, with margin to spare. The drive itself is the longest of the trip (about 3 hr 15, more with summer traffic; build in a 30–45 min buffer beyond that), and the one genuinely demanding stretch: the SS163 coast road is narrow, cliff-hugging, and has blind hairpin turns with tour buses coming the other way. **This is the one leg where "not comfortable driving narrow cliff roads at night" actually matters** — a smaller/compact rental is a real asset here, not just a preference; wide SUVs struggle to pass oncoming buses on some bends. There's no toll road shortcut around this — the SS163 is the route.

**Parking:** Positano's center is car-free, and there's no single unified municipal parking site the way San Gimignano has — each lot is independently run. The two closest to the center are **De Gennaro** and **Mandara**, both around €8–10/hour. *(Source: [Locauto](https://www.locautorent.com/en/blog/how-to-get-to-positano-traffic-restricted-traffic-zone-and-parking/), [Positano.com](https://www.positano.com/en/e/parking-on-amalfi-coast))* Leave the car at whichever has space and walk (or take the local bus/boat) from there.

**Day 5:** Arrive, settle in, walk down to the beach for a late-afternoon swim. Dinner: **[Ristorante Bruno](https://www.brunopositano.it/)**, a cliff-hugging terrace with sunset views over the town and the sea, family-run since 1959 — worth booking specifically for that view, 3–4 weeks ahead in peak season per multiple current guides. **Hours weren't consistently confirmed across sources (reports range roughly 11:30 AM–11:30 PM, with no clear weekly closure day found)** — book via the [official reservation page](https://ristorante-bruno.resos.com/booking) and confirm current hours there rather than trusting this page.

**Day 6:** The do-nothing day, properly — a half-day boat trip along the coast (weather permitting) is the local way to see Positano the way it's actually meant to be seen: from the water, looking up. For a quieter morning stop, **[Chiesa di Santa Maria Assunta](https://chiesapositano.it/en/opening-hours-and-visits/)** — the church with Positano's landmark majolica-tiled dome — is open daily, **9:30 AM–noon and 4:00–8:00 PM**, free entry (a small fee applies only for the crypt/Roman-villa excavation beneath it). Go early morning or late afternoon; it hosts weddings and services, especially on weekends. *(Source: [Chiesa Positano — official visiting hours](https://chiesapositano.it/en/opening-hours-and-visits/))* Afternoon free for the beach or the town's steep, narrow shopping streets. Recommended departure the next morning: **9:00 AM** for the roughly 1-hour drive to Naples airport — adjust to your actual flight time, but separately budget a roughly 2-hour arrival cushion before your flight (distinct from the drive-time buffer above) for rental drop-off, the parking-lot walk, and airport processing.

**Staying at:** Positano · [Map](https://www.openstreetmap.org/search?query=Positano%2C%20Italy)

### Lodging options — Positano (illustrative, not a live quote)

| | Option A | Option B | Option C |
|---|---|---|---|
| **Type** | Clifftop hotel, sea-view terrace | Mid-tier B&B, short walk to beach | Small hotel, upper town |
| **Illustrative nightly range** | €350–420 | €160–190 | €190–220 |
| **Pros** | The view — genuinely worth it for one or two nights | Closest to the beach, best value for the location | Quieter, better parking access nearby |
| **Cons** | Steep steps everywhere, least accessible option | No sea view, smaller rooms | 10+ minute walk down to the water (and back up) |

*Same note as above — illustrative, not a live search result.*

---

## Practical notes carried through from the draft (not decorative)

- **Driving:** an automatic-transmission compact car, booked well ahead — automatics are less common and cost more in Italy, and a compact matters specifically for the Amalfi Coast leg.
- **Parking:** none of the town centers on this route allow cars in; every stop above has a named, linked parking option and a fallback if it's full.
- **ZTL zones:** Positano and San Gimignano both have restricted traffic zones (ZTL) in their historic cores — driving into one without authorization risks an automatic fine by camera. Confirm your hotel's guidance on where exactly you're allowed to drop luggage before driving in.
- **Timing the Amalfi leg:** deliberately scheduled to arrive before dusk, given the stated driver comfort constraint — see the departure-time recommendation above.
- **Reservations:** book Ristorante Belforte and Ristorante Bruno ahead, not on arrival — both are named as needing it in current guides, not just generically "popular."
- **Payment/connectivity:** no time zone change or border crossing on this route, and card payment is standard at all named venues above; small family-run spots off this list may still be cash-preferred, worth carrying some euros regardless.

## Data freshness

This is a fictional, illustrative example built to demonstrate the plugin's output. **The lodging prices are placeholders — not a live search result** — a real run of `lodging-search` returns three actual, dated listings per stop from your confirmed booking sites, each labeled with the date they were retrieved and a reminder to verify before booking, since prices change. **The parking, restaurant, and opening-hours information above was verified via web search** while building this example (sources linked inline) — but hours, prices, and closures can still shift after the fact, so confirm anything time-sensitive before you travel.

## Photo credits

- San Gimignano skyline — Silvia1411, [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0), via [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:San_Gimignano_-_Citt%C3%A0_fortificata_-_2023-09-30_11-51-02_001.jpg)
- Positano view — Alexis Lours, [CC BY 4.0](https://creativecommons.org/licenses/by/4.0), via [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:View_of_Positano.jpg)
- Vernazza harbor — Mike Norton, [CC BY 2.0](https://creativecommons.org/licenses/by/2.0), via [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Vernazza,_Cinque_Terre,_Italy.jpg)
