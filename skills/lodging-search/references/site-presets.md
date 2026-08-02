# Site Presets

Ready-made booking-site lists for Step 0 of `lodging-search`. Present these by name when a preset looks like a fit; never apply one silently.

## Global default

Use when the user has no stated regional or payment preference. Broadest inventory, works for most travelers.

- booking.com
- expedia.com
- hotels.com
- airbnb.com
- agoda.com

## Russian OTAs (MIR-card compatible)

Use when the user mentions a MIR debit/credit card, a Russian billing address, or explicitly asks for Russia-friendly booking sites. These are commonly usable from Russia and support MIR at checkout for many (not all) listed properties — always verify payment method per-listing, since some EU/international properties on these sites only accept "pay on arrival" for MIR cards, not prepayment.

- ostrovok.ru
- hotels.ru
- travelata.ru
- level.travel
- aviasales.ru/hotels
- tinkoff.ru/travel

Fallback if these return nothing usable for a stop (only with the user's standing approval to fall back): airbnb.com, booking.com — label results from these distinctly, since MIR-payment fit is less certain there.

## US default

- booking.com
- expedia.com
- hotels.com
- airbnb.com
- vrbo.com (for larger groups / whole-home stays)

## Europe default

- booking.com
- expedia.com
- airbnb.com
- hrs.com

## Adding your own preset

A preset is just a named list of domains plus one line on why it exists (a payment method it supports, a region it favors, a property type it's strong at). To add one:

1. Give it a clear name describing who it's for (e.g. "Points-redemption hotels only," "Southeast Asia budget stays").
2. List the domains.
3. Note any payment or verification caveat specific to that list, the way the Russian-OTA preset above flags the prepay/pay-on-arrival split.
4. Add it to this file so it's available for Step 0 in future runs.

A fully custom, one-off list (not saved as a preset) is also always valid — just tell the assistant which sites to use for this search.
