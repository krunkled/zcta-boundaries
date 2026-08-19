# ZCTA boundaries

US Census ZIP Code Tabulation Area (ZCTA) polygons, published as static files so
mapping tools can load boundaries from a URL.

## National files — start here

| File | Format | Features | Size | Property |
|---|---|---|---|---|
| `usa_zcta.topojson` | TopoJSON | 33,791 | 8.4 MB | `ZCTA5CE20` |
| `usa_zcta.geojson` | GeoJSON | 33,791 | 9.4 MB | `ZCTA5CE20` |

Both cover every ZCTA in the United States, so nothing needs to be selected ahead
of time — a map can colour whichever ZIPs its data happens to contain, anywhere in
the country.

Prefer the **TopoJSON**. It carries meaningfully more shape detail for less bytes,
because shared borders between neighbouring ZIPs are stored once instead of twice.
The GeoJSON is the same source simplified much harder to squeeze under a 10 MB
ceiling, and exists only for tools that can't parse TopoJSON.

Source: [Census cartographic boundary file](https://www2.census.gov/geo/tiger/GENZ2020/shp/cb_2020_us_zcta520_500k.zip)
`cb_2020_us_zcta520_500k` (2020 vintage, 1:500,000), simplified with
[mapshaper](https://github.com/mbloch/mapshaper). Public domain (US Government work).

## Per-metro subsets

The `zips_<id>.geojson` files are working-set subsets built from the older 2010
ZCTA release, carrying the property `ZCTA5CE10`. The national files supersede them
— they cover more ZIPs (one subset went from 709 to 1,347 matched) and need no
curation. Kept for reference only.

## Why size matters

Whole-state 2010 ZCTA files run 20–77 MB and several mapping tools cap a remote
boundary file at 10 MB, which is what drove both the subsetting and the
simplification levels chosen here.

## Usage

Files are served directly over HTTPS, with no redirects, so tools that refuse to
follow redirects resolve them fine:

```
https://raw.githubusercontent.com/krunkled/zcta-boundaries/main/usa_zcta.topojson
https://raw.githubusercontent.com/krunkled/zcta-boundaries/main/usa_zcta.geojson
```

Join your data to the `ZCTA5CE20` property (5-digit ZIP as a string, zero-padded).
