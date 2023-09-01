# S1S2-Water: A global dataset for semantic segmentation of water bodies from Sentinel-1 and Sentinel-2 satellite images
This repository provides tools to work with the [S1S2-Water dataset](ZENODO-LINK).

[S1S2-Water dataset](ZENODO-LINK) is a global reference dataset for training, validation and testing of convolutional neural networks for semantic segmentation of surface water bodies in publicly available Sentinel-1 and Sentinel-2 satellite images. The dataset consists of 65 triplets of Sentinel-1 and Sentinel-2 images with quality checked binary water mask. Samples are drawn globally on the basis of the Sentinel-2 tile-grid (100 x 100 km) under consideration of pre-dominant landcover and availability of water bodies. Each sample is complemented with STAC-compliant metadata and Digital Elevation Model (DEM) raster from the Copernicus DEM.

If you use our dataset please cite the following publication:

> Wieland, M., Fichtner, F., Martinis, S., Krullikowski, C., Plank, S., Motagh, M. (in review). S1S2-Water: A global dataset for semantic segmentation of water bodies from Sentinel-1 and Sentinel-2 satellite images. Journal of Selected Topics in Applied Earth Observations and Remote Sensing.

## Dataset access
The dataset (~170 GB) is available for download at: ZENODO-LINK

## Dataset information
Each file follows the naming scheme sentinel12_SENSOR_SAMPLE-ID_LAYER.tif (e.g. `sentinel12_s1_5_img.tif`). Raster layers are stored as Cloud Optimized GeoTIFF (COG) and are projected to Universal Transverse Mercator (UTM).

| Sensor | Layer |Description | Values | Format | Bands |
| - | - | - | - | - | - |
| S1 | IMG | Sentinel-1 image <br> GRD product | Unit: dB (scaled by 10000) | GeoTIFF <br> 10980 x 10980 px <br> 2 bands <br> UInt16 | 0: VV <br> 1: VH
| S2 | IMG | Sentinel-2 image <br> L1C product | Unit: TOA reflectance (scaled by 10000) | GeoTIFF <br> 10980 x 10980 px <br> 6 bands <br> UInt16 | 0: Blue <br> 1: Green <br> 2: Red <br> 3: NIR <br> 4: SWIR1 <br> 5: SWIR2
| S1 / S2 | MSK | Annotation mask <br> Hand-labelled water mask | 0: No Water <br> 1: Water | GeoTIFF <br> 10980 x 10980 px <br> 1 band <br> UInt8 | 0: Water mask
| S1 / S2 | VALID | Valid pixel mask <br> Hand-labelled valid pixel mask | 0: Invalid (cloud, cloud-shadow, nodata) <br> 1: Valid | GeoTIFF <br> 10980 x 10980 px <br> 1 band <br> UInt8 | 0: Valid mask
| COPDEM30 | ELEVATION | Copernicus DEM elevation | Unit: Meters | GeoTIFF <br> 3660 x 3660 px <br> 1 band <br> Float32 | 0: Elevation
| COPDEM30 | SLOPE | Copernicus DEM slope | Unit: Degrees | GeoTIFF <br> 3660 x 3660 px <br> 1 band <br> Float32 | 0: Slope

## Data preparation
The following splits images and masks for a desired sensor (Sentinel-1 or Sentinel-2) into training, validation and testing tiles with predefined shape and band combination (**--split**). Slope information can be appended to the image band stack if required.

```python
$ python s1s2_water.py --settings settings.toml
```

Data preparation parameters are defined in a settings TOML file (**--settings**)

```toml
SENSOR = "s2"                           # prepare Sentinel-1 or Sentinel-2 data ["s1", "s2"]
TILE_SHAPE = [256, 256]                 # desired tile shape in pixel
IMG_BANDS_IDX = [0, 1, 2, 3, 4, 5]      # desired image band combination
SLOPE = true                            # append slope band to image bands
EXCLUDE_NODATA = true                   # exclude tiles with nodata values
DATA_DIR = "/path/to/data_directory"    # data directory that holds the original images
OUT_DIR = "/path/to/output_directory"   # output directory that stores the prepared train, val and test tiles

# Sentinel-1 image bands
# {"VV": 0, "VH": 1}

# Sentinel-2 image bands
# {"Blue": 0, "Green": 1, "Red": 2, "NIR": 3, "SWIR1": 4, "SWIR2": 5}
```

## Installation
```shell
$ conda env create --name s1s2_water --file environment.yml
$ conda activate s1s2_water
```