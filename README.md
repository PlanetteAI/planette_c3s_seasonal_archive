<img src="planette_icon.png" alt="Planette Logo" width="400"/>

# Planette's C3S Seasonal Forecast Data

## Overview

The C3S seasonal forecast dataset provides global, daily, probabilistic forecasts of the Earth system, enabling users to assess the likelihood of future climate states. These forecasts are particularly valuable for studying slowly evolving climate patterns such as El Niño, La Niña, and the North Atlantic Oscillation (NAO), which can be predicted with greater skill than the chaotic atmosphere.

This dataset is derived from the Copernicus Climate Change Service (C3S) archive and includes SEAS5 hindcasts (1981-2016) and forecasts (2017-present) at 1°x1° global resolution. More models from the C3S archive will be updated as they are processed into cloud native format.

The planette C3S archive stores this data in cloud native format for easy access and analysis using Zarr format with icechunk for efficient cloud access.

## Variables

### Atmospheric Variables at Pressure Levels

| Variable                | Units     | Pressure Levels (hPa)     | Description                                 |
|-------------------------|-----------|---------------------------|---------------------------------------------|
| u                       | m/s       | 10, 200, 500, 850  | Zonal wind at pressure levels               |
| v                       | m/s       | 10, 100, 200, 500, 850  | Meridional wind at pressure levels          |
| t                       | K         | 100, 200, 300, 400, 500, 700, 850, 925 | Temperature at pressure levels            |
| q                       | kg/kg     | 100 200, 300, 400, 500, 700, 850, 925, 1000    | Specific humidity at pressure levels        |
| z                       | m²/s²     | 10, 200, 300, 500, 700, 850 | Geopotential |

- Variables at unavailable pressure levels are filled with NaNs.  


### Surface and Column Variables

| Variable                | Units     | Description                                 |
|-------------------------|-----------|---------------------------------------------|
| t2m                     | K         | 2 meter temperature                         |
| t2m_max                 | K         | Daily maximum 2 meter temperature           |
| t2m_min                 | K         | Daily minimum 2 meter temperature           |
| t2d                     | K         | 2 meter dew point temperature               |
| pr                      | kg m⁻² s⁻¹| total precipitation rate                    |
| sst                     | K         | Sea surface temperature                     |
| sic                     | K         | Sea surface temperature                     |
| stl1                    | K         | Soil temperature (layer 1)                  |
| slp                     | hPa       | Sea level pressure                          |
| tcwv                    | kg/m²     | Total column water vapor                    |
| u10                     | m/s       | 10 meter zonal wind                         |
| v10                     | m/s       | 10 meter meridional wind                    |
| u10m                    | m/s       | 10 meter zonal wind                         |
| v10m                    | m/s       | 10 meter meridional wind                    |
| ws10m                   | m/s       | 10 metre wind speed                         |
| tau_x                   | N/m²      | Surface wind stress (zonal)                 |
| tau_y                   | N/m²      | Surface wind stress (meridional)            |
| sf                      | kg m⁻² s⁻¹| Equivalent liquid water snowfall rate       |
| sdwe                    | kg m⁻².   | Snow depth water equivalent                 |
| cdd                     | K·day     | Cooling degree days                         |
| hdd                     | K·day     | Heating degree days                         |
| dswrf                   | J m⁻²     | Surface shortwave radiation downwards       |
| olr                     | J m⁻²     | TOA outgoing longwave radiation             |



## Temporal Coverage

- **SEAS5 Hindcasts:** 1981–2016
- **SEAS5 Forecasts:** 2017–present
- (Additional models may be added in the future.)

## Spatial Coverage

- **Global** coverage at **1°x1°** resolution

## Data Format and Access

- **Format:** Zarr (written with [icechunk](https://github.com/earth-mover/icechunk))
- **Storage:** Amazon S3 (`s3://planette-c3s-seasonal-forecasts/seas5_ic/`)
- **Organization:** Data is organized by forecast periods (hindscast: 1981-2016; forecast: 2017-present), and variable groups (single / pressure).  
  Example:  
  `s3://planette-c3s-seasonal-forecasts/seas5_ic/hindcast/`  

### Data Structure

```
s3://planette-c3s-seasonal-forecasts/seas5_ic/
├── {forecast period}/
│   └── {variable group}/
│       └── 1latx1lon/ 
```

## Getting Started

### Prerequisites

Install the required Python packages:
NOTE: to access data install icechunk > 2.0

```bash
pip install xarray zarr s3fs icechunk
```

### Quick Start Example

```python
import xarray as xr
import icechunk as ic

# get the bucket and prefix
year = 2025 # Forecasts are stored by year, and available from 1981 to present
variable = "t2m" # 2 meter temperature (K)
bucket = "planette-c3s-seasonal-forecasts"
prefix = f"seas5_ic/hindcast"
group = "single/1platx1plon".    \# or pressure


storage = ic.s3_storage(bucket=bucket, prefix=prefix, region="us-east-2", anonymous=True). 
repo = ic.Repository.open(storage). 
session = repo.readonly_session("main")  

# Open the dataset
ds = xr.open_zarr(session.store, group=group, consolidated=False, decode_timedelta=True, chunks={})

# Explore the data
print(ds)
```

For a complete tutorial, see the [Jupyter notebook tutorial](c3s_seasonal_forecast_tutorial.ipynb) in this repository.

## Usage Notes

- Data is provided as-is from the C3S archive.
- Please cite the Copernicus Climate Change Service (C3S) and relevant publications (see below).
- License: [Copernicus Licence](https://cds.climate.copernicus.eu/api/v2/terms/static/licence-to-use-copernicus-products.pdf) (similar to CC-BY-4.0).

## Contact

For questions about this dataset or the Planette C3S archive:
- **Email:** aodhan.sweeney@planette.ai
- **Organization:** Planette.ai

## References

- Copernicus Climate Change Service (C3S) (2017): C3S seasonal forecast data. [CDS Dataset](https://cds.climate.copernicus.eu/cdsapp#!/dataset/seasonal-original-single-levels)
- Johnson, S. J., et al. (2019). SEAS5: The new ECMWF seasonal forecast system. *Geoscientific Model Development*, 12(3), 1087–1117. [https://doi.org/10.5194/gmd-12-1087-2019](https://doi.org/10.5194/gmd-12-1087-2019)
- [C3S Seasonal Forecasts Documentation](https://climate.copernicus.eu/seasonal-forecasts)

## Acknowledgments

This work is made possible by open data provided by ECMWF and by the Copernicus Climate Change Service (C3S). The data is also held and made available in cloud native format via a partnership between Planette and Amazon. 
