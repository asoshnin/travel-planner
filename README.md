# Travel Planner

<p align="center">
  <img src="assets/banner.svg" alt="Travel Planner — live lodging search and printable itineraries for Claude Cowork and Claude Code" width="100%">
</p>

<p align="center">
  <img alt="MIT License" src="https://img.shields.io/badge/license-MIT-2b2825?style=flat-square">
  <img alt="No API keys required" src="https://img.shields.io/badge/API%20keys-not%20required-a8462f?style=flat-square">
  <img alt="Claude Cowork | Claude Code" src="https://img.shields.io/badge/Claude-Cowork%20%7C%20Code-6b6259?style=flat-square">
</p>

Two skills that fix the two ways AI trip-planning usually falls apart: hotel prices that are hallucinated or missing entirely, and itineraries that read like a bare list of links instead of something you'd actually want to follow.

---

## See it in action first

Here's what a single prompt produces:

> "Plan a 6-day road trip landing in Pisa, hitting Cinque Terre, Tuscany, and the Amalfi Coast, flying out of Naples. Two adults, relaxed pace, boutique/small hotels, mid-range budget, English."

**Sample output** (rendered from the prompt above):

![Rendered itinerary example](examples/assets/screenshot-top.png)

The narrative, the lodging comparisons, and the day-by-day structure all come out of that one prompt — no manual formatting, no separate hotel-tab-juggling.

> **This particular trip is a fictional demo, not the author's real vacation** (his actual budget currently tops out at a weekend camping trip, tent included). Every price shown is illustrative, not a live quote — if this repo gets enough stars, maybe someday it'll fund the real thing. Full example is in [`examples/`](examples/).

**Click through to one full stop** (Tuscany, Day 3–4 — photo, narrative, and lodging comparison together):

<p align="center">
  <a href="https://htmlpreview.github.io/?https://github.com/asoshnin/travel-planner/blob/main/examples/dream-vacation-example.html">
    <img src="examples/assets/screenshot-preview.png" alt="Click to open the full rendered itinerary example" width="440">
  </a>
</p>

<p align="center"><i>Click the image to open the live rendered page (opens in a new tab, same file that ships in this repo).</i></p>

A real run searches your confirmed booking sites live and returns three actual, dated listings per stop in this same format — never a bare `[PENDING]` placeholder.

---

## What's in this plugin

| Skill | What it does |
|---|---|
| `lodging-search` | Searches live booking sites (a global default, a named regional/payment preset, or your own custom list) for each overnight stop and returns up to 3 positioned options with pros/cons — or an honest, labeled estimate if live search isn't possible. Never returns a bare "pending" placeholder. |
| `travel-itinerary-builder` | Interactively confirms your traveler profile (dates, pace, budget, output language, and more), then builds a narrative-rich, print-friendly, static HTML itinerary with real lodging cards, licensed and attributed photos, and link-based maps — no API keys required. Every stop also gets a recommended departure time, a parking note, and named restaurants/landmarks with verified opening hours, not just generic categories. |

## Why this exists

Most AI-generated itineraries either hallucinate hotel prices or give up on live search entirely and leave a placeholder. `lodging-search` is built specifically to fail *honestly*: it distinguishes a cookie-consent popup from a CAPTCHA from a genuinely empty market, retries sensibly, and always returns either a real result or a clearly labeled estimate — never a silent gap. `travel-itinerary-builder` then makes sure that data, plus the narrative you actually wrote, survives all the way into the final page instead of getting flattened into a bare link list — and it applies the same honesty rule to the logistics that actually get you through a travel day: a recommended departure time (not just a duration), a named parking option, and named restaurants/landmarks with real opening hours, or an explicit "not confirmed" label instead of a plausible-sounding guess.

## Requirements

- Claude Cowork, or Claude Code with browser-automation tooling available (`mcp__claude-in-chrome__*` or `mcp__computer-use__*`, or an equivalent live-browser tool)
- No API keys required — lodging search uses your existing browser access, and maps use OpenStreetMap link-outs

## Installing

**Claude Cowork:** install from [claude.com/plugins](https://claude.com/plugins/) once listed, or load the `.plugin` file directly.

**Claude Code:** if this plugin is listed in a marketplace you've added, install it the standard way:
```
claude plugin marketplace add <marketplace-source>
claude plugin install travel-planner@<marketplace-name>
```
Otherwise, clone this repo and point Claude Code at the local `.claude-plugin` folder — see the [Claude Code plugin docs](https://docs.claude.com/en/docs/claude-code/plugins) for the current local-install steps.

## Using it

Just describe your trip:

> "Plan a 10-day road trip from Amsterdam through the Alps to Lake Como and back, 2 adults, relaxed pace, self-catering apartments preferred."

The itinerary builder will ask a short set of clarifying questions (dates, budget, output language, etc.) before drafting anything, then hand off to lodging search for each overnight stop once the plan is locked. You can also invoke either skill on its own — e.g. "find me 3 hotel options in Lisbon for next weekend" will trigger `lodging-search` directly. More sample prompts (short, single-purpose ones) are in [`examples/`](examples/).

### Choosing a booking-site scope

By default, `lodging-search` uses mainstream global sites (Booking.com, Expedia, Airbnb, etc.). If you have a payment-method constraint or a regional preference, say so — for example "only search sites that work from China" — and it will offer a matching preset, or you can just name the exact sites you want it to use. See [`skills/lodging-search/references/site-presets.md`](skills/lodging-search/references/site-presets.md) for the built-in presets and how to add your own.

---

## If this saved you some time

A star helps other people find it, and if you've built a useful regional or payment-method site preset, a pull request adding it to `references/site-presets.md` is genuinely useful — that's the part most likely to need knowledge neither of us has alone.

## Contributing

Issues and pull requests welcome.

## License

MIT — see [`LICENSE`](LICENSE).
