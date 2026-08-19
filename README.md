# ZCTA boundaries

US Census ZIP Code Tabulation Area (ZCTA) polygons as GeoJSON, published as static
files so mapping tools can load boundaries from a URL.

Each file is a `FeatureCollection` of `MultiPolygon` features carrying a single
property:

| Property | Meaning |
|---|---|
| `ZCTA5CE10` | 5-digit ZCTA code — the join key |

## Why subsets

Whole-state ZCTA files run 20–77 MB. Several mapping tools cap a remote boundary
file at 10 MB, so each file here covers only a working set of ZIPs rather than a
whole state. Coordinates are rounded to 5 decimal places (~1 m) and vertices
thinned at a ~56 m tolerance, which keeps roughly 400–900 vertices per ZIP.

Thinning is distance-based rather than Douglas–Peucker: these rings are closed
(first point == last), which makes the RDP baseline degenerate and collapses
every ring to two points.

## Source

Derived from the 2010 Census ZCTA boundaries published at
[OpenDataDE/State-zip-code-GeoJSON](https://github.com/OpenDataDE/State-zip-code-GeoJSON).
Public domain (US Government work).

## Usage

Files are served directly over HTTPS:

```
https://raw.githubusercontent.com/krunkled/zcta-boundaries/main/zips_<id>.geojson
```

No redirects, so tools that refuse to follow them resolve these fine.
