# Geospatial Analysis (GIS / Remote Sensing)

Use for: satellite/aerial imagery, vector/raster GIS data, coordinate reference systems,
spatial statistics, terrain and hydrological analysis, and cloud-native geospatial workflows.

## When to use

- The task involves `.shp`/`.geojson`/`.tif`/`.gpkg` files, lat/lon data, or map output.
- Imagery processing (Sentinel, Landsat, MODIS, SAR, hyperspectral) or classification over it.
- Questions about projections, reprojection, or "why don't these two datasets line up."

## Core concepts to get right every time

- **Coordinate reference systems (CRS).** Always check and explicitly state the CRS of every
  input before combining datasets. The single most common geospatial bug is silently mixing
  a geographic CRS (degrees, e.g. EPSG:4326) with a projected CRS (meters, e.g. a local UTM
  zone) — distances and areas computed in degrees are meaningless.
- **Raster vs. vector.** Rasters (imagery, elevation) are grids of pixels with a resolution and
  CRS; vectors (points/lines/polygons) are discrete geometries with attributes. Don't reach for
  raster math when the data is really discrete features, or vice versa.
- **Resolution and resampling.** State the pixel size (ground sample distance) and the
  resampling method (nearest/bilinear/cubic) whenever rasters are reprojected or resized —
  nearest for categorical/classification rasters, bilinear/cubic for continuous values.

## Typical toolchain (Python)

- Vector: `geopandas`, `shapely`
- Raster: `rasterio`, `rioxarray`, `xarray`
- Cloud-native discovery: STAC catalogs (`pystac-client`), Cloud-Optimized GeoTIFFs (COGs)
- Spatial stats / ML: `scikit-learn`/`pysal` for spatial autocorrelation and clustering
- Visualization: `folium`/`matplotlib` + `cartopy` for static or interactive maps

## Workflow

1. Identify each input's CRS and native resolution before writing any processing code.
2. Reproject everything to a single common CRS appropriate to the analysis (a projected CRS in
   meters for area/distance calculations; keep geographic CRS only for simple display).
3. Clip to the shared area of interest before heavy raster math — avoids wasted compute on
   out-of-bounds pixels.
4. For classification or ML over imagery, keep a held-out validation set that is spatially
   separated from training data (random pixel splits leak spatial autocorrelation and inflate
   accuracy).
5. Validate outputs visually (quick-look map) before trusting derived statistics — geospatial
   bugs are usually obvious on a map and easy to miss in a table of numbers.

## Pitfalls

- Treating degrees as if they were a uniform distance unit (a degree of longitude shrinks
  toward the poles).
- Forgetting nodata/mask values when computing raster statistics (they silently skew means).
- Mixing pixel-center vs. pixel-corner conventions when georeferencing.

## Quick API reference (rasterio / geopandas)

```python
import rasterio
import geopandas as gpd

# Read a raster, always check CRS and nodata before computing anything
with rasterio.open("raster.tif") as src:
    data = src.read(1)
    crs = src.crs
    nodata = src.nodata
    transform = src.transform          # pixel -> CRS coordinate mapping

# Windowed read for files too large to load whole
with rasterio.open("large.tif") as src:
    subset = src.read(1, window=((0, 1000), (0, 1000)))

# Reproject/clip a raster to a vector AOI's CRS before combining
aoi = gpd.read_file("aoi.geojson")
aoi_matched = aoi.to_crs(crs)          # match raster CRS, not the other way around for area math

# Write out with an explicit profile (driver, dtype, crs, transform all required)
with rasterio.open("out.tif", "w", driver="GTiff", height=data.shape[0],
                    width=data.shape[1], count=1, dtype=data.dtype,
                    crs=crs, transform=transform) as dst:
    dst.write(data, 1)
```

## Escalating beyond this file

This covers CRS/resolution judgment calls, not the full GDAL/rasterio/geopandas API surface
(STAC search, COG optimization, terrain/hydrology algorithms, 500+ worked examples across 8
languages). If a dedicated geospatial skill/reference is installed and available, defer to it
for that depth; see `escalation-and-scope.md`.
