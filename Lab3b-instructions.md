# Lab 3b: Converting to GeoJSON and styling choropleths

## TGIS 503, Fall 2021, Dr. Emma Slager

### Introduction

In this part of lab 3, you'll practice converting data from Shapefile to GeoJSON so that it can be displayed in a web map. Additionally, you'll practice adapting instructions from an online tutorial to fit the data and visualization goals of a similar but distinct mapping project. 

*Update (11/10/21):* After our check-in about this lab in class this week, I've decided to change the deliverables for this lab. You will be required to complete Part 1 of the instructions below (converting from Shapefile to GeoJson, using both ArcGIS and Mapshaper, and comparing the two tools), but Part 2 is now optional. You may complete the choropleth map for extra credit points, but you do not need to. 

### Part 1: Converting from Shapefile to GeoJSON

From Canvas, download the Zip folder named '2020_Census_Tracts.' This folder contains a shapefile of Census tracts in Pierce County, with basic demographic and housing information for the Census tracts in its attribute table. Eventually, you'll be making a choropleth map of the housing vacancy rate by Census tract. But first, you need to get this shapefile into a useable GeoJSON file so that we can import it into a Leaflet map. 

Follow these basic steps, consulting your lecture notes and any help forums or guides you might find by searching the web as needed: 

1. Extract (decompress) the Zip folder so that you can load the data into ArcMap or ArcPro.
2. In ArcMap or ArcPro, reproject the shapefile so that it is in the **WGS84 Geographic Coordinate System.** Remember that there are Projected Coordinate Systems that use the WGS84 datum, but we need our data to be unprojected so that the coordinate information for each feature is readable to Leaflet as lat/long coordinate pairs, rather than as projected coordinate pairs. 
3. Still in ArcMap or ArcPro, use the 'Features To JSON' tool to export your reprojected shapefile as a GeoJSON. Be sure to check the box to save the output as a GeoJSON. Save the file in a location you can find it again. 
4. Validate your file by uploading it (open > file) to [geojson.io](https://geojson.io/). If the Pierce County census tracts appear over the basemap, you've successfully converted your file--congrats! If the layer doesn't appear, you've made a mistake and should consult your notes and try again. 

If you've successfully converted and validated your file, you could go ahead and load it into Leaflet. However, we're going to try another way of converting the file and compare the results. 

Mapshaper is an online converter and editor for map data. It's a piece of JavaScript software originally developed by [Matthew Bloch](https://github.com/mbloch), graphics editor at the New York Times. 

Visit [Mapshaper.org](https://mapshaper.org/) and follow these basic steps, consulting your lecture notes and any help forums or guides you might find by searching the web as needed: 

1. Upload your reprojected (the one using WGS84) shapefile of Pierce County Census tracts to the site, using drag and drop or the `select` button. Note: you must upload the .shp file *and all of its supporting files* (.dbf, .shx, .prj, etc.), not just the .shp. 
2. Leave the options at their defaults and hit `import`
3. Use the `Simplify` button to simplify the geometry of the file. Consult the [Mapshaper documentation](https://github.com/mbloch/mapshaper/wiki) as needed to understand the options here. You should simplify the geometry to at least the level of 10% of vertices remaining but can go even farther if you wish. 
4. Export your simplified file to GeoJSON and save the file in a location you can find it again. Give it a name that differentiates it from the GeoJSON file you produced with ArcGIS. 
5. Validate your file using [geojson.io](https://geojson.io/) as you did with your first GeoJSON. If it doesn't appear, figure out what you did wrong and correct your mistakes. 

In your write up, answer the following questions: 

* Using your file manager (e.g. Windows File Explorer or MacOS Finder), find the the size of the two GeoJSON files you've just created. What are their sizes? Based only on this information, which do you think is better to use in your web map and why? 
* Describe which method (ArcMap export or Mapshaper web converter) you prefer for converting Shapefiles to GeoJSON, and justify your answer. (There is no correct answer here; I just want you to reflect on which workflow <u>you</u> prefer.) Your answer doesn't need to be longer than a brief paragraph. 


### Part 2: creating an interactive choropleth (optional, for extra credit)

In this part of the lab, you'll take one of the GeoJSONs you've just created and make a choropleth map of the rate of housing vacancy in each Census tract in Pierce County. To do this, you should draw on [this tutorial from Leaflet](https://leafletjs.com/examples/choropleth/), making changes as needed to make the map work with your data, rather than the data provided by Leaflet. 

The main learning goal here is for you to figure out how to adapt existing tutorials to meet your goals and work with your data, so I won't provide too much further guidance. But to help just a little, here are a couple of hints and reminders: 

* One thing to note is that the tutorial brings the geojson data in slightly differently than we've been doing it. Rather than use an AJAX request to load the geojson file, it formats [the GeoJSON](https://leafletjs.com/examples/choropleth/us-states.js) as a JavaScript file that contains a variable called `statesData,` and the value of `statesData` is set to contain the GeoJSON. You are welcome to bring your data into your map using either this method or the AJAX method you used in Lab 2--whichever you prefer. 
* The property that you'll want to use for styling your polygons is called 'vac_rate' (standing for vacancy rate). It is a percent, so values can range from 0-100. 
* You can decide how many data classes you want to use for your choropleth; it may be useful to examine the data in ArcGIS again to decide where to put the breaks between classes. Also consider what classification method you want to use (i.e. Jenks natural breaks, equal interval, quantiles, etc.). 
* [ColorBrewer](https://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3) is a great tool for making color choices. 
* Use appropriate variable names for the data at hand. 

Your final map should resemble the tutorial, in that it should be a choropleth where hovering over a Census tract results in an outline appearing around that tract and makes the vacancy rate of that Census tract appear in an info control somewhere on the map. 

### What to submit

For Part 1, please submit a write-up with the answers to the questions in Part 1. If you do not complete the optional Part 2, please also submit the two GeoJson files you created. 

If choose to complete Part 2, please also submit a link to your map. (If you do this, I will assess the GeoJson file you chose to use from the map, so you do not need to separately submit your GeoJson files.)



