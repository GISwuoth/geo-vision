# Flood Mapping with Sentinel-1 SAR Imagery

Detecting flood extent from open Synthetic Aperture Radar (SAR) data using a pre/post-event change detection approach in Google Earth Engine.

## Overview

This project uses Sentinel-1 SAR imagery to map flood extent in a flood-prone area near Lake Victoria, Kenya (Budalangi/Yala floodplain region). SAR is well suited to flood mapping because radar backscatter penetrates cloud cover and senses surface roughness — open water (calm, flat) returns much lower backscatter than land, making flooded areas stand out clearly in VV polarization, even during the rainy season when optical imagery is unusable.

## Methodology

1. **Region of Interest (ROI):** A bounding box over a flood-prone area in western Kenya (lon 34.5–34.9, lat -0.1–0.3).
2. **Data source:** Sentinel-1 GRD (`COPERNICUS/S1_GRD`) collection, accessed via Google Earth Engine.
   - Mode: Interferometric Wide (IW)
   - Orbit pass: Descending
   - Polarization: VV
3. **Pre-event composite:** Mean VV backscatter, 1–10 April 2020 (before the flood).
4. **Post-event composite:** Mean VV backscatter, 10–20 April 2020 (after the flood).
5. **Change detection:** A difference image is computed as `post_event − pre_event`.
6. **Flood extent extraction:** Pixels where the difference falls below a threshold of **-2.5 dB** are classified as flooded (a drop in backscatter indicates a change from rough/vegetated land surface to smooth open water). The result is self-masked so only flooded pixels are shown.
7. **Visualization:** Pre-event, post-event, and flood extent layers are rendered on an interactive `geemap` map.

## Requirements

- Python 3.x
- A Google Earth Engine account (registered and authenticated)
- Packages:
  ```bash
  pip install earthengine-api geemap matplotlib
  ```

## Usage

1. Authenticate with Earth Engine (first run only):
   ```python
   ee.Authenticate()
   ```
2. Update `ee.Initialize(project='your-project-id')` with your own GEE project ID.
3. Run the script. An interactive map will display:
   - Pre-event VV backscatter
   - Post-event VV backscatter
   - Detected flood extent (blue)

## Notes & Limitations

- The **-2.5 dB threshold** is empirical and was tuned for this site/date range. It should be re-calibrated for other regions, seasons, or incidence angles (e.g. using Otsu thresholding or a histogram-based method for a more defensible cutoff).
- No speckle filtering or terrain (layover/shadow) correction is applied in this version — both are common refinements for production-grade flood maps.
- No permanent water mask is subtracted, so some "flooded" pixels may correspond to pre-existing water bodies rather than new flooding.
- Single orbit pass (descending) and single polarization (VV) are used; combining ascending/descending passes or adding VH would improve robustness.

## Possible Extensions

- Otsu or Kittler–Illingworth automatic thresholding instead of a fixed dB value
- Speckle filtering (e.g. refined Lee, or temporal multi-look averaging across more images)
- Permanent water mask (e.g. JRC Global Surface Water) to isolate *new* flooding
- Export of flood extent as GeoTIFF/shapefile for area statistics and impact assessment
- Validation against optical imagery or ground reports where available

## Data Source

Sentinel-1 SAR GRD, Copernicus Programme (ESA), accessed via [Google Earth Engine](https://earthengine.google.com/).

## Author

Florence Owiti
