# Working-with-Wind-data-NC-file.
#Working with Wind data NC file using GEE and Colab  Reference: https://geemap.org/notebooks/68_netcdf_to_ee/
 



!pip install geemap xarray rioxarray netcdf4 localtileserver

import geemap

url = "https://github.com/giswqs/leafmap/raw/master/examples/data/wind_global.nc"
filename = "wind_global.nc"

geemap.download_file(url, output=filename)

data = geemap.read_netcdf(filename)
data

tif = "wind_global.tif"
geemap.netcdf_to_tif(filename, tif, variables=["u_wind", "v_wind"], shift_lon=True)

geojson = "https://github.com/giswqs/leafmap/raw/master/examples/data/countries.geojson"

import ee
import geemap

# Authenticate and initialize the Earth Engine module.
ee.Authenticate()
ee.Initialize            (project=' xxxxxxxxxxxxxx’  ) # your GEE ID

m = geemap.Map(layer_ctrl=True)
m.add_raster(tif, band=[1], palette="coolwarm", layer_name="u_wind")
m.add_geojson(geojson, layer_name="Countries")
m

m = geemap.Map(layer_ctrl=True)
m.add_netcdf(
    filename,
    variables=["v_wind"],
    palette="coolwarm",
    shift_lon=True,
    layer_name="v_wind",
)
m.add_geojson(geojson, layer_name="Countries")
m

m = geemap.Map(layer_ctrl=True)
m.add_basemap("CartoDB.DarkMatter")
m.add_velocity(filename, zonal_speed="u_wind", meridional_speed="v_wind")
m

##
