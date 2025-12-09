# Geog 328 Final Project: Seattle Transit Walkshed Analysis
### Group AD3: Rayne Thompson, Noah Curtis, Jay Van Lare, Jishnu Kandala
![Favicon image displaying a transit icon over a blue background](../transit-walkshed/assets/favicon.png)

## [Application URL](https://turnert447.github.io/transit-walkshed/html/home.html)

## Project Description
This project is an interactive webmap visualizing population accessibility to Seattle's rapid transit infrastructure. This is not the full picture of Seattle's transit, but we chose to utilize only the Rapid Ride BRT lines and Link light rail station in order to get the picture of accessibility to Seattle's fastest transit. The project performs real-time spatial analysis in Turf.js, allowing users to explore walksheds at three different distance thresholds that roughly equate to a 5, 10, and 15 minute walk. Dynamic choropleths display within the selected buffer to visualize population density and show the user the total population, area, and percentage of Seattle's population that live within the selected walkshed. The project also contains a 2050 view, visualizing planned transit expansions alongside projected population growth.

## Project Goal
Fast, frequent transit within walking distance is an important factor in building sustainable cities and incentivizing the population to use alternative transportation options. This tool is intended for planners, policymakers, and advocates to dynamically visualize Seattle's transportation needs in the present and future and illustrate the impact of planned transit expansions. The added option to toggle between RapidRide and Link infrastructure adds the element of visualizing the strengths and weaknesses of coverage in each system and identifying priority areas for new expansion. 

## User-Facing Features and Functions

#### Walkshed Distance Selection
Radio buttons display for different walkshed distances, 0.25 mi (5 min walk), 0.5 mi (10 minute walk) and 0.75 mi (15 minute walk). These buttons are tied to an event listener that updates the currentBuffer variable and triggers the recalculateWalkshed() function. The createUnifiedBuffer function creates a turf.buffer() at the selected distance, merging with turf.union() to create a single walkshed polygon
![Walkshed Distance Buttons](../transit-walkshed/assets/screenshots/walkshed-distance.png)

#### Transit Layer Toggles
User-facing checkboxes allow the user to show/hide the transit lines independently, returning a combined feature collection of the selected features. Upon deselecting a line, the visibility is updated to hide the layer, and recalculateWalkshed() fires upon repressing the Calculate Walkshed button to return the new population information.

#### Stop Point Visibility
This checkbox controls the visibility of the individual transit stop markers on the map. The toggle calls updateStopLayerVisibility(), which shows or hides the circle layers for stops. This does not trigger any recalculation since the stops are purely visual. 

![Transit Lines & Stop Points](../transit-walkshed/assets/screenshots/lines-stops.png)

#### Reset Button
The "Clear" button has an event listener tied to the "clear" function, which clears the buffer feature selection and repopulates the full city choropleth and statistics.
![Clear Button](../transit-walkshed/assets/screenshots/clear-but.png)


#### Interactive Popups
Clicking on census tracts of transit stops enables informational popups. The map registers click events and extracts properties from the JSON, rendering them in a Mapbox GL popup. Census popups display population, density, area, and growth rate, while stops display the stop name, type, and ID. 
![Census Popup](../transit-walkshed/assets/screenshots/census-pop.png)
![Stop Popup](../transit-walkshed/assets/screenshots/stop-pop.png)

#### Real-time Statistics
The statistics panel displays the total walkshed population, the percentage of Seattle's total population contained within, and the area of the walkshed. These stats are populated by the updateStats() function called via the event listener on the calculate button. Hitting "clear" returns the city to the default full rendering, showing the full choropleth and 100% of the population and giving users immediate feedback for different transit combinations and buffer distances. 

![Statistics Window](../transit-walkshed/assets/screenshots/stats.png)

## Other Functions
#### Spatial Analysis
The core spatial analysis takes place in the clipCensusToBuffer() function that processes each census tract against the walkshed boundary. To quickly reject distant tracts, a bounding box is the first function. Then a boolean disjoint is utilized to save resources on the intersection function. Once intersecting tracts are calculated, turf.intersect() clips the tract geometry to the walkshed boundary and estimates population using the area-weighted calculateClippedPopulation() function that assumes even population distribution in the intersecting tract.

#### Performance Caching
To avoid redundant spatial calculations, the application caches computed walkshed results. getCacheKey() generates a string based on the current selection state, and is called within the recalculateWalkshed() function to see if that combination already exists. If it is cached, it can immediately update the map without having to recompute. 

#### Temporal Comparison
Part of our data pre-processing was calculating the percentage population change at the census block level and dissolving them into their aggregate in each tract, allowing us to pull an estimated population change for each tract despite boundaries changing and making that a difficult spatial analysis. This estimated data allowed us to apply the percentage change to return an estimate of Seattle's 2050 population and enabled the population projection data.

## Data Sources

- [Sound Transit Open Transit Data](https://www.soundtransit.org/help-contacts/business-information/open-transit-data-otd/otd-downloads):  Link Light Rail stations and alignments
- [King County Metro GTFS Feed](https://kingcounty.gov/en/dept/metro/rider-tools/mobile-and-web-apps): Current Rapid Ride stops and alignments
- [Seattle Open Data](https://data.seattle.gov/): Seattle City Limits for data clipping, as well as planned Rapid Ride lines
- [US Census Bureau Population Data](https://www.census.gov/geographies/mapping-files.html): census tract and block data for population rendering and projection

## Applied Libraries, Web Services, and Tools

#### Data Preprocessing
- [Jupyter](https://jupyter.org/)
- [QGIS](https://qgis.org/)

#### Mapping & Visualization
- [Mapbox GL JS](https://docs.mapbox.com/mapbox-gl-js/guides/)
- Custom Mapbox basemap utilizing the Dark theme

#### Spatial Analysis
- [Turf.js](https://turfjs.org/docs/intro): Buffer, union, intersect, area calculation

#### Web Services
- Github Pages: Static hosting for app and JSON files
- Mapbox API: Tile serving

#### Acknowledgements
We would like to acknowledge the great professor Bo Zhao and all of his dedicated TA's for being strong and helpful proponents of our learning and development as geographers. We'd like to thank Sound Transit, King County Metro, the City of Seattle, and the US Census Bureau for providing high-quality data for free for our use in this project, as well as the open source communities of QGIS, turf.js, and Jupyter for maintaining the tools we used to complete this project. 

#### AI Use Disclosure
After building the base functionality manually including the basic buffer analysis, website structure, and choropleth rendering, AI was consulted to optimize and enhance the spatial analysis. It was primarily utilized for optimization. The caching functionality was written by AI, as well as a rewrite of the buffer clip and calculation logic to make it less computationally expensive. It was consulted for CSS and styling to make the base webpages more interactive and appealing, as well as extensive debugging use throughout the coding process. AI was also utilized during the data pre-processing phase to give advice on processing workflow and Python syntax for calculating the population projection. All AI output was studied for full understanding, reviewed and modified by the team members. 


