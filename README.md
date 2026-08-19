# ZCTA boundaries

US Census ZIP Code Tabulation Area (ZCTA) polygons, published as static files so
mapping tools can load boundaries from a URL. Every file covers **all 33,791 ZCTAs
in the United States** — nothing is selected ahead of time, so a map can colour
whichever ZIPs its data happens to contain, anywhere in the country.

## Proximity-graded files — use these

`usa_zcta_<id>.topojson` — same national coverage, but vertex density is graded by
distance from a particular area of interest. Detail is spent where someone actually
zooms in rather than spread evenly across the country.

| Distance from the area of interest | Vertices retained |
|---|---:|
| Inside / touching | **100%** (untouched Census geometry) |
| ≤ 30 mi | ~99% |
| ≤ 80 mi | ~99% |
| ≤ 200 mi | ~80% |
| ≤ 600 mi | ~24% |
| Rest of country | ~12% |

Each lands at 8.7–9.3 MB, under the 10 MB ceiling that mapping tools commonly place
on a remote boundary file. The far bands are scaled automatically until the file
fits, so the near bands never pay for coverage elsewhere.

**Grading is per arc, not per polygon.** TopoJSON stores a border shared by two ZIPs
exactly once, as a single arc. Simplifying arcs independently therefore cannot open
slivers or gaps between neighbours — both polygons reference the same simplified arc,
so their shared edge stays identical by construction. Simplifying per polygon and
merging afterwards does not survive this: the two sides of a band boundary disagree.
Arc endpoints are never moved, since those are the junctions where three or more
polygons meet.

Measured on a grid around ZCTA 85634, counting points no polygon covers:

```
uniform 5% simplification   596
proximity-graded            587
full Census (truth)         587
```

The graded file matches the source exactly in the near field.

## Uniform files

`usa_zcta.topojson` (8.4 MB) and `usa_zcta.geojson` (9.4 MB) apply one simplification
level everywhere. Use them when there is no particular area of interest. Prefer the
TopoJSON: it carries more shape detail per byte, because shared borders are stored
once instead of twice. The GeoJSON is simplified four times harder to fit the same
ceiling and exists only for tools that cannot parse TopoJSON.

## About the holes

Census only creates a ZCTA where people receive mail, so uninhabited land genuinely
has no polygon — military ranges, some BLM acreage, parts of national monuments. A
gap in these files is usually real rather than a rendering fault. Compare against the
source before assuming otherwise.

## Per-metro subsets (superseded)

`zips_<id>.geojson` are working-set subsets built from the older 2010 ZCTA release,
carrying the property `ZCTA5CE10`. The national files replace them — they cover more
ZIPs (one subset went from 709 to 1,347 matched) and need no curation. Kept for
reference.

## Source

[Census cartographic boundary file](https://www2.census.gov/geo/tiger/GENZ2020/shp/cb_2020_us_zcta520_500k.zip)
`cb_2020_us_zcta520_500k` (2020 vintage, 1:500,000), processed with
[mapshaper](https://github.com/mbloch/mapshaper). Public domain (US Government work).

## Usage

Served directly over HTTPS with no redirects, so tools that refuse to follow
redirects resolve them fine:

```
https://raw.githubusercontent.com/krunkled/zcta-boundaries/main/usa_zcta.topojson
https://raw.githubusercontent.com/krunkled/zcta-boundaries/main/usa_zcta_<id>.topojson
```

Join your data to the `ZCTA5CE20` property — 5-digit ZIP as a zero-padded string.
Note that PO-box ZIPs have no ZCTA at any vintage and therefore never appear.
