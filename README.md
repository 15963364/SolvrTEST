# Holladay Land Eval Map

A single-file Leaflet map (`index.html`) for evaluating three residential land targets in Holladay, Utah.

```sh
npx serve .
# open http://localhost:3000
```

## What it does

- **Parcels**: queries the Utah LIR parcel FeatureServer for a ~600 m box around 40.64878, -111.82808 (paginated, `outSR=4326`, `f=geojson`). The county `Parcels_SaltLake_LIR` layer is a fallback. Click a parcel to see its ID, address, and acreage, and to tag it.
- **Targets**
  - *Parcel B*: auto-matched on address `5520 … COTTONWOOD`.
  - *Lund pair*: the parcel containing the pin plus the touching neighbor closest to N/NE, dissolved into one polygon.
  - *Parcel A*: tag it by clicking. Dashed orange outlines mark likely-vacant 1.4–2.0 ac candidates.
  - Tags (A / B / Lund / Exclude) live in `localStorage` and override the auto matches.
- **Canal**: pulls USGS NHD flowlines (MapServer layer 6, then 5) and keeps canal/ditch features (FType 336), preferring ones named "East Jordan". If nothing usable comes back, use **Trace canal** to click the line in yourself (also saved in `localStorage`).
- **Analysis (all ESTIMATES)**
  - Canal buffer: 20 ft from the centerline (HCC 13.76.400), shaded red.
  - Setbacks: in the `ZONING` config object, with citations. Front = 40 ft for lots over 1.5 ac, otherwise an editable neighborhood average. Side = % of lot width. Rear = editable.
  - Envelope = parcel minus per-edge setback strips minus the canal buffer (Turf.js). Each edge is classed as front/side/rear from the lot's front direction, which is auto-detected from unparceled street frontage or set by hand per target.
- **Basemaps**: Esri World Imagery, Esri World Topo, and Imagery + Esri Hillshade at 50%.
- **Export**: a KML with parcels, targets, canal, buffer, and envelopes in the same colors, for Google Earth and onX.

## Known limits / verify before relying on it

- **Rear setback**: the code uses a lot-size sliding scale (HCC 13.14.052). The value for lots over 1 acre could not be verified when this was built, so 40 ft is a placeholder. Edit it in the panel.
- The side-setback section number (13.14.053) and percentages come from the City's R-1 permit worksheet. Confirm them against the current code.
- Corner-lot rules, private-driveway setbacks, height corridor, easements, and slopes are not modeled.
- Parcel lines are GIS approximations. The real canal setback is measured from a surveyed annual high-water line / top of bank, not the centerline.
