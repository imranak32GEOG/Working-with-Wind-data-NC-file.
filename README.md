!pip install leafmap
!pip install xarray rioxarray netcdf4 localtileserver


from google.colab import output
output.disable_custom_widget_manager()


!pip install --upgrade leafmap xarray rioxarray netcdf4 localtileserver


# Install necessary packages
!pip install leafmap xarray rioxarray netcdf4 localtileserver

# Import required modules
import leafmap
import rioxarray

# Define data source
url = "https://github.com/opengeos/datasets/releases/download/raster/wind_global.nc"
filename = "wind_global.nc"

# Download data
leafmap.download_file(url, output=filename, overwrite=True)

# Read the NetCDF file
data = leafmap.read_netcdf(filename)

# Convert NetCDF to GeoTIFF
tif = "wind_global.tif"
leafmap.netcdf_to_tif(filename, tif, variables=["u_wind", "v_wind"], shift_lon=True)

# Define GeoJSON for overlay
geojson = "https://github.com/opengeos/leafmap/raw/master/examples/data/countries.geojson"

# Create Map
m = leafmap.Map(layers_control=True)

# Add raster layer with u_wind variable
m.add_raster(tif, indexes=[1], palette="coolwarm", layer_name="u_wind")

# Add GeoJSON overlay
m.add_geojson(geojson, layer_name="Countries")

# Display map
m


m = leafmap.Map(layers_control=True)
m.add_basemap("CartoDB.DarkMatter")
m.add_velocity(
    filename,
    zonal_speed="u_wind",
    meridional_speed="v_wind",
    color_scale=[
        "rgb(0,0,150)",
        "rgb(0,150,0)",
        "rgb(255,255,0)",
        "rgb(255,165,0)",
        "rgb(150,0,0)",
    ],
)
m

import leafmap
import xarray as xr
import os
import imageio

# Load wind data using xarray (replace with the path to your NetCDF file)
data = xr.open_dataset("/content/wind_global.nc")

# Initialize the map
m = leafmap.Map()
m.add_basemap("CartoDB.DarkMatter")

# Add velocity data
m.add_velocity(
    data=data,
    zonal_speed="u_wind",
    meridional_speed="v_wind",
    color_scale=[
        "rgb(0,0,150)",  # Blue (Low speed)
        "rgb(0,150,0)",  # Green
        "rgb(255,255,0)",  # Yellow
        "rgb(255,165,0)",  # Orange
        "rgb(150,0,0)",  # Red (High speed)
    ],
)

# Save the map as an HTML file
html_path = "wind2_velocity_map.html"
m.to_html(outfile=html_path)

print(f"Map saved as HTML at {html_path}. You can download it and view it in a browser.")
