# Bike Trip Prediction Model

## Description
The goal of this project is to develop a model to predict the number of bike trips that occur on any given target streets in New York City. The model leverages open Citi Bike data and the MapBox Directions API to generalize trips routes via a random forest model. The impetus for this project is an advocacy effort to improve bike and pedestrian infrastructure along two streets in Brooklyn: Dean Street and Bergen Street.

## Installation
Prerequisites can be found in `requirements.txt`. These notebooks should work with most distributions and versions of the main libraries (pandas, sklearn).

## Usage
### Notebooks
- `et.ipynb`: Extracts and transforms Citi Bike data. Generates the main dfs to be used in the project.
- `directions.ipynb`: Buckets all bike trips into a 10x10 geographic grid. Samples trips from this grid and requests [MapBox Directions](https://docs.mapbox.com/api/navigation/directions/) for sampled trips.
- `model.ipynb`: Trains a random forest model and predicts the total number of trips on your target streets.

### Instructions
1. Download [Citi Bike data](https://s3.amazonaws.com/tripdata/index.html). I downloaded `2023-citibike-tripdata.zip` and exported it the root folder. If you wish to use later data, you will need to update the directory names in all the notebooks.
2. Run `et.ipynb`. This notebook: 
  - Extracts all trip data and compiles it into a single large df of ALL Citi Bike trips (~35M rows). Saved as `2023_data.pkl`
  - Compiles the unique trips into a single df. Saved as `unique_trips.pkl`
3. Run `directions.ipynb`, this notebook samples from the unique trips the `sample_size` you define. It uses your `mapbox_api_key` to request directions. It then checks for your `target_streets` in the directions that are returned.
- Results are stored as `sampled_trips.pkl`
4. Run `model.ipynb`, this notebook takes the results from your samples and trains a random forest model to predict whether a trip contains your target streets based on the start and end points.

### Notes
- *On sampling:* Simply randomly selecting trips would likely result in many similar trips being sampled. I.e. The sample would probably contain many trips from Downtown to Midtown because these trips are common. Since we need the model to generalize based on geographic coordinates, we need to sample a geographically diverse set of trips. To accomplish this, I split the city into a 10x10 grid, then sample randomly from each grid cell for start and end points.
- *On class sizes:* Most likely, trips with your target street(s) are a small minority of all unique trips (<1% even). We use SMOTE to generate new, similar samples to balance the class sizes. A `k_neighbors` value of 2 or 3 works well.
- *On Citi Bike:* Once the model is trained, we use it to predict the unique trips that would've taken our target streets. We then join this to the entire dataset to get the count of total trips. Finally, [according to NYCDOT](https://www.nyc.gov/office-of-the-mayor/news/847-23/mayor-adams-dot-commissioner-rodriguez-lyft-expansion-improvements-citi-bike-system) Citi Bike makes up less than 25% of all bike trips. So we multiply our result by `4` to get total trips.

## License
MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.