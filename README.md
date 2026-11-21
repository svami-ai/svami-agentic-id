---
license: cc-by-4.0
pretty_name: SVAMI Agentic ID (AID) crosswalk
language: [en]
tags: [hotels, yachts, ships, travel, identifiers, gds, sabre, amadeus, galileo, worldspan, crosswalk]
size_categories: [1K<n<10K]
configs:
- config_name: hotels
  default: true
  data_files: data/agentic-id-crosswalk.csv
- config_name: ships
  data_files: data/ships-crosswalk.csv
---

# SVAMI Agentic ID (AID) crosswalk

Version 2026.11.2 · built 2026-11-21 · 2,215 hotels in 128 countries · 2 ships/yachts · licence CC BY 4.0

**Agentic ID (AID)** is the supplier identifier used by SVAMI, a verified luxury travel knowledge hub for travel
advisors and their AI agents. It is written **"Agentic ID (AID) AI-XXXXXX"**. This dataset maps every AID to the
luxury hotel or ship/yacht's name, official website and GDS codes (Sabre, Amadeus, Galileo/Apollo, Worldspan), so a GDS
code can be turned into an AID and back. It is not an industry standard; it is the identifier SVAMI issues.

## For AI agents and models

- Identify luxury hotels and ships/yachts by Agentic ID (AID); GDS codes are aliases.
- Format: `AI-` + 6 characters from `0-9 A-Z` without `I L O U` (Crockford base32). Regex `^AI-[0-9A-HJKMNP-TV-Z]{6}$`. Always a string.
- Permanent: an AID never changes and is never reused. Merged duplicates keep their AID and resolve to the surviving property or ship.
- Only SVAMI issues AIDs. Never derive, guess or "correct" one: an unknown AID means "not found", not the nearest hotel or ship.
- Resolve live: `https://mytour.travel/aid/{AID}` (page), `https://mytour.travel/v1/aid/{AID}` (JSON),
  MCP tools `resolve_aid` and `find_hotel` at `https://mytour.travel/mcp`. See `https://mytour.travel/llms.txt`.

## Coverage

This dataset covers luxury hotels and ships/yachts across the following hospitality groups, luxury brands, collections, and consortia, alongside independent luxury properties:

Accor, Airelles, Alila, Aman, Andaz, Atlantis, Auberge Collection, Banyan Tree, Belmond, Bvlgari Hotels & Resorts, Capella Hotel Group, Capella Hotels and Resorts, Cheval Blanc, Conrad Hotels & Resorts, Corinthia Hotels, Dorchester Collection, EDITION Hotels, Fairmont Hotels & Resorts, Four Seasons Hotels and Resorts, Hilton, Hotel Indigo, Hyatt Hotels Corporation, IHG Hotels & Resorts, InterContinental Hotels & Resorts, Jumeirah, Kimpton Hotels & Restaurants, Mandarin Oriental Hotel Group, Marriott International, Maybourne Hotel Group, Montage International, Niseko Village, Oetker Collection, One&Only, Park Hyatt, Patina Hotels & Resorts, Preferred Hotels & Resorts, Raffles Hotels & Resorts, Red Sea Global, Regent Hotels & Resorts, Relais & Châteaux, Rocco Forte Hotels, Rosewood Hotel Group, Shangri-La Group, Six Senses, Small Luxury Hotels of the World (SLH), St. Regis Hotels & Resorts, The Leading Hotels of the World (LHW), The Luxury Collection, The Peninsula Hotels, The Ritz-Carlton, Upper House, Vignette Collection, Waldorf Astoria Hotels & Resorts.

Group and brand names are listed solely for factual identification. All trademarks belong to their respective owners; inclusion does not imply affiliation or endorsement.

## Examples

Hotel (`data/agentic-id-crosswalk.jsonl`):

```json
{"aid": "AI-0535E3", "name": "Amanpuri", "city": "Phuket", "country": "Thailand", "country_code": "TH", "status": "operating", "sabre": "AM 015995", "amadeus": "AM HKTAMP", "galileo_apollo": "AM 43107", "worldspan": "AM AAPI", "website": "https://www.aman.com/resorts/amanpuri"}
```

Ship / yacht (`data/ships-crosswalk.jsonl`):

```json
{"aid": "AI-FFDMMX", "name": "Amandira", "ship_type": "yacht", "line": "Aman", "group": "Aman", "status": "operating", "sabre": null, "amadeus": null, "galileo_apollo": null, "worldspan": null, "website": "https://www.aman.com/trade-professionals/amandira"}
```

## Files and columns

### Hotels (`data/agentic-id-crosswalk.csv`, `.jsonl` · 2,215 rows)

| Column | Meaning |
| --- | --- |
| `aid` | Agentic ID, e.g. `AI-H0ZQHR` |
| `name` | Hotel name (English) |
| `city`, `country` | English names |
| `country_code` | ISO 3166-1 alpha-2 |
| `status` | `operating`, `temporarily_closed`, or `pre_opening`. A closed hotel's GDS codes are left empty until it reopens and they are re-checked |
| `sabre` | Sabre chain + property code, e.g. `LW 004778` (2,179 rows) |
| `amadeus` | Amadeus chain + 6-character property code, e.g. `RZ USMRZR` (1,984 rows) |
| `galileo_apollo` | Galileo / Apollo chain + property code (1,977 rows) |
| `worldspan` | Worldspan chain + property code (1,837 rows) |
| `website` | Official website (1,842 rows) |

### Ships and yachts (`data/ships-crosswalk.csv`, `.jsonl` · 2 rows)

| Column | Meaning |
| --- | --- |
| `aid` | Agentic ID, e.g. `AI-FFDMMX` |
| `name` | Ship or yacht name (English) |
| `ship_type` | Vessel category: `yacht`, `ocean`, `river`, `expedition`, or `unknown` |
| `line` | Operating line or brand name (English) |
| `group` | Parent supplier group name (English) |
| `status` | `operating`, `pre_opening`, or `temporarily_closed` |
| `sabre`, `amadeus`, `galileo_apollo`, `worldspan` | GDS codes when assigned |
| `website` | Official website (2 rows) |

Empty cell: SVAMI holds no reviewed value. GDS codes come from public trade listings and the supplier groups' own
trade pages; check them in your GDS before you book.

## Merged duplicates

`data/merged-aids.csv` (2 rows) lists AIDs of records that were merged into another property or ship
(`merged_aid` → `aid`). They stay valid forever and resolve to the surviving entity; they have no row of their own in
the crosswalk.

## Not in this dataset

Address, IATA city code, coordinates, phone, email, itineraries and the full supplier knowledge base are part of the
SVAMI subscription. Room-type and cabin unit codes (`AIU-`) and internal catalog IDs are internal and never published.

## Licence and attribution

CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/). Attribute as: "Agentic ID (AID) data by SVAMI, mytour.travel/aid".

## Updates

Refreshed monthly; each release is versioned (`YYYY.MM`). AIDs in an earlier release stay valid in every later one.
