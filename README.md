# Travel Planner

Two Claude Cowork / Claude Code skills for planning multi-stop road trips: live lodging search across a booking-site scope you control, and a polished, printable, multilingual HTML itinerary builder with embedded licensed photos and real lodging comparisons.

## What's in this plugin

| Skill | What it does |
|---|---|
| `lodging-search` | Searches live booking sites (a global default, a named regional/payment preset, or your own custom list) for each overnight stop and returns up to 3 positioned options with pros/cons — or an honest, labeled estimate if live search isn't possible. Never returns a bare "pending" placeholder. |
| `travel-itinerary-builder` | Interactively confirms your traveler profile (dates, pace, budget, output language, and more), then builds a narrative-rich, print-friendly, static HTML itinerary with real lodging cards, licensed and attributed photos, and link-based maps — no API keys required. |

## Why this exists

Most AI-generated itineraries either hallucinate hotel prices or give up on live search entirely and leave a placeholder. This plugin's `lodging-search` skill is built specifically to fail *honestly*: it distinguishes a cookie-consent popup from a CAPTCHA from a genuinely empty market, retries sensibly, and always returns either a real result or a clearly labeled estimate — never a silent gap. `travel-itinerary-builder` then makes sure that data (plus the narrative you actually wrote) survives all the way into the final page instead of getting flattened into a bare link list.

## Requirements

- Claude Cowork, or Claude Code with browser-automation tooling available (`mcp__claude-in-chrome__*` or `mcp__computer-use__*`, or an equivalent live-browser tool)
- No API keys required — lodging search uses your existing browser access, and maps use OpenStreetMap link-outs

## Installing

**Claude Cowork:** install from [claude.com/plugins](https://claude.com/plugins/) once listed, or load the `.plugin` file directly.

**Claude Code:**
```
claude plugin marketplace add anthropics/claude-plugins-community
claude plugin install travel-planner@claude-community
```

## Using it

Just describe your trip:

> "Plan a 10-day road trip from Amsterdam through the Alps to Lake Como and back, 2 adults, relaxed pace, self-catering apartments preferred."

The itinerary builder will ask a short set of clarifying questions (dates, budget, output language, etc.) before drafting anything, then hand off to lodging search for each overnight stop once the plan is locked. You can also invoke either skill on its own — e.g. "find me 3 hotel options in Lisbon for next weekend" will trigger `lodging-search` directly.

### Choosing a booking-site scope

By default, `lodging-search` uses mainstream global sites (Booking.com, Expedia, Airbnb, etc.). If you have a payment-method constraint or a regional preference, say so — for example "I only have a MIR card" or "only search sites that work from Brazil" — and it will offer a matching preset, or you can just name the exact sites you want it to use. See `skills/lodging-search/references/site-presets.md` for the built-in presets and how to add your own.

## License

MIT — see `LICENSE`.

## Contributing

Issues and pull requests welcome. If you add a useful regional/payment site preset, please contribute it back to `references/site-presets.md` rather than keeping it local — that's the part most likely to need community knowledge neither of us has alone.
