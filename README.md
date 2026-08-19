# ZCTA boundaries

US Census ZIP Code Tabulation Area (ZCTA) polygons, published as static files so mapping
tools can load boundaries from a URL. Every file carries all **33,791 ZCTAs** in the
United States, so a map can colour whichever ZIPs its data contains, anywhere.

Boundaries are **unmodified Census geometry**. Nothing is filled, merged, or reshaped.

## Proximity-graded files

`usa_zcta_<id>.topojson` — national coverage with vertex density graded by distance from
a particular area of interest, so detail is spent where someone actually zooms in:

| Distance from the area of interest | Vertices retained |
|---|---:|
| Inside / touching | **100%** (untouched Census geometry) |
| ≤ 30 mi | ~99% |
| ≤ 80 mi | ~99% |
| ≤ 200 mi | ~80% |
| ≤ 600 mi | ~12–25% |

Each lands at 8.6–9.4 MB, under the 10 MB ceiling mapping tools commonly place on a
remote boundary file. The far bands scale automatically until the file fits, so the near
bands never pay for coverage elsewhere.

Grading is **per arc, not per polygon**. TopoJSON stores a border shared by two ZIPs once,
as a single arc, so simplifying arcs independently cannot open slivers between neighbours
— both polygons reference the same simplified arc. Arc endpoints are never moved, since
those are the junctions where three or more polygons meet.

## Uniform files

`usa_zcta.topojson` (8.4 MB) and `usa_zcta.geojson` (9.4 MB) apply one simplification
level everywhere — use when there is no particular area of interest. Prefer the TopoJSON;
the GeoJSON is simplified four times harder to fit the same ceiling and exists only for
tools that cannot parse TopoJSON.

## About the gaps

**ZCTAs do not tessellate, and that is by design.** Census builds them from census blocks
by most-common ZIP and [intentionally leaves unassigned](https://www.census.gov/programs-surveys/geography/guidance/geo-areas/zctas.html)
uninhabited land and water over two square miles. Measured with a geometry engine,
**23,823 sq mi of Arizona — 20.8% of the state — belongs to no ZCTA.** Those gaps are
reservations, military ranges and federal land: the Tohono O'odham Nation, Fort Apache
Indian Reservation, and the Yuma Test Range account for the largest.

Do not fill them. Altering the shapes breaks alignment with ACS and decennial data, which
is the entire reason to use ZCTAs. Render them over a basemap that shows public land
(OpenStreetMap labels reservations and protected areas) so the gaps explain themselves.

## USPS ZIPs that have no ZCTA

A USPS ZIP code is not a shape — it is a collection of delivery routes — and roughly
9,190 of them (PO boxes, large-volume customers) have no ZCTA at all. Handle those
**relationally, not geometrically**, with the official
[HRSA ZIP-to-ZCTA crosswalk](https://data.hrsa.gov/DataDownload/GeoCareNavigator/ZIP%20Code%20to%20ZCTA%20Crosswalk.xlsx),
which folds each into the surrounding tabulation area. Across one 15-client working set
that resolved 420 of 443 otherwise-unmappable ZIPs without touching a boundary.

## Source

[Census cartographic boundary file](https://www2.census.gov/geo/tiger/GENZ2020/shp/cb_2020_us_zcta520_500k.zip)
`cb_2020_us_zcta520_500k` (2020 vintage, 1:500,000), processed with
[mapshaper](https://github.com/mbloch/mapshaper). Public domain (US Government work).

## Usage

Served directly over HTTPS with no redirects, so tools that refuse to follow redirects
resolve them fine:

```
https://raw.githubusercontent.com/krunkled/zcta-boundaries/main/usa_zcta.topojson
https://raw.githubusercontent.com/krunkled/zcta-boundaries/main/usa_zcta_<id>.topojson
```

Join on the `ZCTA5CE20` property — 5-digit ZIP as a zero-padded string.

## Superseded

`zips_<id>.geojson` are per-metro subsets from the older 2010 ZCTA release, property
`ZCTA5CE10`. The national files replace them: more ZIPs matched (one subset went 709 →
1,347) and no curation needed. Kept for reference only.
