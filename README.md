# Lab 2, Part 1: Introduction to web mapping with Leaflet

## Why Leaflet?

[Leaflet](https://leafletjs.com/) is one of many different frameworks that exist for making interactive web maps. I like it because it is free and open-source, is lightweight and flexible, and can be easily combined with other tools to expand its capabilities. Also, you can build Leaflet maps without signing up for any kind of account, which makes it an ideal framework for new learners.

This first part of Lab 2 will walk you through setting up a Leaflet map, adding data from a live data feed maintained by the USGS, making interactive map markers, and styling the data with proportional symbols. In Part 2 of the lab, you'll go through these steps on your own, mapping data of your choosing and adding additional functionality that suits the goals of your particular map. 

## A basic Leaflet map

<iframe src="https://ejslgr.github.io/Leaflet-Intro/code-samples/basic.html" style="width:100%; height:500px;"></iframe> [View this example on its own](https://ejslgr.github.io/Leaflet-Intro/code-samples/basic.html)

The map above is a very simple Leaflet map. You can pan, zoom, and click it, but it only requires a few lines of code that include:

1. An HTML page with `<head>` and `<body>` elements
2. A link to the Leaflet CSS styles
3. A link to the Leaflet JavaScript library
4. A `<div>` element to hold the map
5. A height style specified for the map div
6. A bit of JavaScript to create the map in the div

View the code below with comments explaining what each part does:

```
<html>
<head>
	<title>Intro to Leaflet</title>
 	<!-- the Leaflet CSS -->
	<link rel="stylesheet" href="https://unpkg.com/leaflet@1.5.1/dist/leaflet.css"/>
	<!-- the Leaflet JavaScript library -->
	<script src="https://unpkg.com/leaflet@1.5.1/dist/leaflet.js"></script>
</head>

<body>
  	<!-- code where we create and name the container that holds the map -->
	<div id="map" style="height: 95%"></div>
  	<!-- code where we build the map and its functionality -->
	<script>
		var mymap = L.map('map').setView([47.258, -122.465], 12);
		var basemap = L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/terrain/{z}/{x}/{y}{r}.png', {
			attribution: 'Map tiles by <a href="http://stamen.com">Stamen Design</a>, <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a> &mdash; Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
		}).addTo(mymap);
		var point = L.marker([47.258, -122.465]).addTo(mymap);
			point.bindPopup("<b>Hello world!</b><br>I am a popup.");
  	</script>
</body>
</html>
```

Let's take a closer look at the JavaScript code that creates the map. Inside the <script> tags, we have code that:

1. Creates the map variable (named "`mymap`")
2. Uses `L.map()` to initialize the map object, passing it the id of the div where we want the map to go
3. Uses the `setView()` method to center the map on Tacoma (latitude 47.258 and longitude -122.465) and set the zoom level to 12
4. Uses `L.tileLayer()` to create the base layer of map tiles, specifying a URL template for the tile images. In this case, we're using tiles map designed by a company called Stamen, but we could use tiles from [many different sources](http://leaflet-extras.github.io/leaflet-providers/preview/), including Open Street Map, ESRI, or Carto. {z}/{x}/{y} is a template that Leaflet uses to find tiles at the correct zoom, x, and y coordinates. The code inside the `attribution` option sets the attribution text that appears in the bottom right corner of the map.
5. Uses the `addTo()` method to add the base tile layer to the map
6. Uses `L.marker()`to create a point marker at lat/long 42.258, -122.465 and `addTo()` to add the point marker to the map
7. Uses `.bindPopup` to create a popup that appears when the point marker is clicked. We use HTML `("<b>Hello world!</b><br>I am a popup.")` to provide the content of the popup.

## Try it yourself

To complete the rest of Lab 2 Part 1, copy the code from this [starterfile ](https://github.com/ejslgr/Leaflet-Intro/blob/master/code-samples/earthquakes-starter.html), paste it into a new HTML document in Atom, and follow along to make the changes yourself.

You'll note that the starter file is very similar to the basic Leaflet map we looked at above, except that we've removed the marker and changed the center and zoom level. Next, we'll add some data.

## Working with GeoJSON data

Adding data with the `L.marker()` method is simple, but it can be somewhat inconvenient. If we were mapping hundreds of points, we would have to manually type in the lat/long pairs for every point into our code. No thank you! Can't we just add a Shapefile?

Shapefiles, as you no doubt know, are the default vector data format when working with ArcGIS. With web mapping, however, the standard data format is GeoJSON. Like other formats for geospatial data, GeoJSON stores information about geographic features and their non-spatial attributes (e.g. a line indicating a street and the name of the street). It is based on JavaScript Object Notation, which means it will be more familiar to web developers than GIS professionals, but it's fairly easy to work with and understand.

Instead of storing data in tables, GeoJSON stores data in "key: value pairs." These are both machine readable and human readable. Here's an example:

```
{ "type": "FeatureCollection",
  "features": [
    { "type": "Feature",
      "geometry": {"type": "Point", "coordinates": [102.0, 0.5]},
      "properties": {"name": "Example Point"}
      },
      
    { "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [102.0, 0.0], [103.0, 1.0], [104.0, 0.0], [105.0, 1.0]
          ]},
      "properties": {
        "name": "example line",
        "number of vertices": 4
        }
      },
      
    { "type": "Feature",
       "geometry": {
         "type": "Polygon",
         "coordinates": [
           [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0],
             [100.0, 1.0], [100.0, 0.0] ]
           ]},
       "properties": {
         "name": "example polygon",
         "number of vertices": 5}
         }
    ]
  }
```

In this file, we have a collection of features. Each feature has a geometry and properties. The geometry describes the geographic feature. For instance, the first feature is a point located at lat: 0.5 and lon: 102.0. The properties are the non-spatial attributes. In this case, each feature has a name, and the line and polygon features also have a property that lists of the number of vertices in the shape.

If you want to know more about GeoJSON, a good place to start is its [Wikipedia page](https://en.wikipedia.org/wiki/GeoJSON). Note that many open data portals make data available to download in the GeoJSON format, but it's also possible to convert data in other formats (like Shapefiles, CSVs, KMLs, etc.) into GeoJSON with various tools.

## Adding GeoJSON data to our Leaflet map

To our map, we're going to add a live GeoJSON feed of all the earthquakes that occurred in the past day. USGS maintains numerous earthquake feeds, and you can see a summary of the information it makes available about these quakes here: https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php

The feed we want is all earthquakes of every magnitude that occurred in the last 24 hours: https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson. If you click on this link, you'll recognize that what you're looking at is a GeoJSON file: a collection of features that have a specified geometry and numerous properties or non-spatial attributes.

Here's the code we use to add the earthquake GeoJSON to our map. In Atom, add the lines of code as indicated, then view your changes in a web browser.

In the `head` of the HTML file, after the line of code where you add the leaflet.js script, insert the following:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
```

This is a link to the JQuery library. JQuery is a common JavaScript library that makes it easy to make changes to a web page. In this case, we'll use it to load our GeoJSON file.

Next, in the `body` of the HTML file, after the code where you initialize the map but before the `</script>` tag closes, add the following:

```
// load GeoJSON from an external file and add it to the map
$.getJSON("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson",function(data){
    L.geoJson(data).addTo(mymap);
});
```

`$.getJSON` loads the file located at the URL that is specified. Then, `L.geoJson()` creates a vector layer from the GeoJSON and adds it to the map with `.addTo()`.

Save your work and open it in your web browser and it should look like this:

<iframe src="https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex1.html" style="width:100%; height:500px;"></iframe> [View this example on its own](https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex1.html)

## Adding interactivity: Let's make those markers clickable

If we hover over the markers, we should see the cursor change from the panning hand to the pointing finger, suggesting that we can click on the markers. However, clicking doesn't seem to do anything. Let's change that. Let's make it so that when we click the markers, we get a pop-up window that tells us the location and magnitude of the earthquake and get a link we can click for more information. Thankfully each of these things (location, magnitude, and further info link) are available as properties in the earthquake GeoJSON.

Change the code that creates the GeoJSON layer as follows, adding the `pointToLayer` option to the `L.geoJson()` method before we add the layer to the map:

```
$.getJSON("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson",function(data){
    L.geoJson(data, {
        pointToLayer: function(feature, latlng){
            var marker = L.marker(latlng);
            marker.bindPopup("hello!");
            return marker;
        }
    }).addTo(mymap);
});
```

`pointToLayer` is an option built into the `L.geoJson()` method that Leaflet uses to determine how to convert a point feature into a map layer. `pointToLayer` always accepts two arguments, the GeoJSON `feature` and `latlng`, which indicates the location of the feature. We then return the features as some kind of Leaflet layer, in this case, a marker layer, specified by `L.marker()`. (Later, we'll experiment with another kind of Leaflet layer, the circle marker, or L.circleMarker.) Each marker appears at the given feature's specified lat,lng location.

Now, when we click on each earthquake marker, we should get a pop-up that reads "hello!" Let's make that text a little more helpful. Change the `marker.bindPopup` line of code as follows:

```
marker.bindPopup("Location: " + feature.properties.place + "<br>Magnitude: " + feature.properties.mag + "<br><a href =" + feature.properties.url +">More info</a>");
```

This code uses HTML to set the content of the marker pop-up. It selects information from GeoJSON using the `feature.properties. `notation to display the 'place,' 'mag,' and 'url' properties for the selected feature. We can reference what these properties are in the [GeoJSON metadata](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php). Now, the markers should be clickable:

<iframe src="https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex2.html" style="width:100%; height:500px;"></iframe> [View this example on its own](https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex2.html)

## Adding some style: proportional symbols

By default, the points are styled with generic blue markers. This is fine, but what if we wanted to style the markers based on some attribute, such as magnitude? We can use JavaScript to make a proportional symbol map using conditional statements.

First, let's change our markers to circle markers. Make the following change to your code. JavaScript is case sensitive, so make sure you capitalize correctly!

```
var marker = L.circleMarker(latlng);
```

By default, these are styled as blue circles with a radius of 10 pixels, and a partly transparent fill. We can change these style defaults with options that are specified within the `L.circleMarker()` method. Make the following change and view the results:

```
var marker = L.circleMarker(latlng, {radius: 2, color: 'red'});
```

Here we've made the radius just 2px across and changed the color of the circle to red. In web mapping, you can [define colors](https://www.w3schools.com/html/html_colors.asp) a number of ways, including with hex codes, rgb values, or---for a limited set of colors--- with standard names. Here we've used a standard name.

Next, let's set the radius of the circle to change based on the magnitude of the earthquake. Make the following change to your code:

```
var marker = L.circleMarker(latlng, {radius: feature.properties.mag, color: 'red'});
```

Here we're pulling our radius value from the magnitude property of the GeoJSON. At the time of my writing this tutorial, there had been a lot of small earthquakes in North America and a smaller number of larger earthquakes in the Pacific and Indian Oceans. What do the patterns look like on the day you're mapping the data?

Currently, the data is not classified, as is typical with proportional symbol maps of numeric data. But what if we were working with ordered categorical data? Let's say we wanted to put the quakes into four categories: tiny (quakes under mag 1), small (quakes between mag 1 and 2.5), medium (between mag 2.5 and 4.5), and large (quakes larger than mag 4.5). We can achieve this with an if/else statement:

```
$.getJSON("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.geojson",function(data){
	// add GeoJSON layer to the map once the file is loaded
		L.geoJson(data, {
			pointToLayer: function(feature, latlng){
                		var radiusSize,
				mag = feature.properties.mag;
                			if (mag > 4.5) radiusSize = 10;
					else if ( mag > 2.5) radiusSize = 6;
                			else if (mag > 1) radiusSize = 4;
					else radiusSize = 2;
                		var marker = L.circleMarker(latlng, {radius: radiusSize, color: 'red'});
				marker.bindPopup("Location: " + feature.properties.place + "<br>Magnitude: " + feature.properties.mag + "<br><a href =" + feature.properties.url +">More info</a>");
				return marker;
			}
		}).addTo(mymap);
});
```

Make these changes, save your work, and view the changes in your web browser:

<iframe src="https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex3.html" style="width:100%; height:500px;"></iframe> [View this example on its own](https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex3.html)

## Adding a title, legend, and explanatory text

Finally, let's practice good cartography and add some vital map elements. We'll add the title and explanatory text outside of the map and in the web page itself. At the very top of the `<body>` of your html document, above the code where you create the div that holds the map, add the following line of code:

```
<h1>Earthquakes that have occurred in the past 24 hours</h1>
```

When you save and preview the changes, you might now have to scroll down a bit to see both the header and the full map. Let's shrink the size of the map div so that this isn't the case. Change the `"height"` attribute of the map div to 80% instead of 95%:

```
	<div id="map" style="height: 80%"></div>
```

Sans-serif fonts are generally easier to read on digital screens than serified fonts, but our text defaults to Times New Roman. Let's change that with some CSS styling. In the `<head>` of the HTML document, beneath the lines of code where we add the Leaflet and JQuery links, add the following:

```
	<!--CSS styles-->
	<style>
	body {
		font-family: sans-serif;
	}
	</style>
```

Next, let's add some explanatory text. In the `<body>` of the HTML document, below the code that creates the map div but above the opening `<script>` tag, add the following:

```
	<p>This map depicts all earthquakes that have occurred in the past 24 hours. Data comes from the <a href="https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php">USGS Live Earthquake Feed</a> and is updated every minute. Earthquakes are visualized with proportional symbols where earthquakes of larger magnitude are depicted with larger circle markers. Click on a marker for more information about the earthquake.</p>
	<p>The Pacific Northwest lies along the Cascadia fault. Tectonic activity along this fault could cause a catastrophic earthquake that would produce major damage throughout our region. Learn about how researchers at the University of Washington are modelling earthquake risk and preparing for disaster response at the <a href="https://hazards.uw.edu/geology/m9/">M9 Project</a>.</p>
```

The `<p>` tag is an HTML element that we use to enclose paragraphs. The `<a>` tag (a for 'anchor') allows us to link to other URLs, which we use here to link to our data source and an external website where users can learn more about earthquake preparedness research in the PNW.

Having text span the entire width of the browser window makes for very short, wide paragraphs. Let's limit the width and center the main content on our page with a little extra CSS styling. Back in the `<head>` inside the `<style>` tags, update the CSS as follows:

```
body {
	font-family: sans-serif;
	max-width: 900px;
	margin: auto;
}
```

Now, our map and the explanatory text will not exceed a width of 900px, no matter how wide the browser screen is. `margin: auto;` also centers the content by automatically adding margins of equal width on either side of the main body content. We could do a whole lot more with CSS to make our page appear more polished and professional, but this is enough for now. Feel free to take this further with more CSS styling if you wish!

<iframe src="https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex4.html" style="width:100%; height:500px;"></iframe> [View this example on its own](https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex4.html)

Finally, let's add a legend. Somewhat counter-intuitively, adding a legend is not particularly easy with Leaflet. Let's hope that some future version release changes that. In the meantime, adding a legend to our map will take four final steps: linking to one more library, creating the legend content in HTML, styling the legend with CSS, and adding the legend with JavaScript. First, in the `<head>` add this line of code below your link the JQuery library and above the `<style>` section:

```
<script src= "https://cdn.jsdelivr.net/npm/leaflet-legend@1.0.2/leaflet-legend.min.js"></script>
```

This library (or 'plugin' as Leaflet calls them) was made by a Leaflet user to make it a bit easier to add legends. This is actually one of the things I love about Leaflet; its user community is empowered and encouraged to make add-ons that increase the functionality of the framework. Next, add the following HTML code to the `<body>` of your document, below the explanatory text and above the `<script>` tag. This code uses SVG (scalable vector graphics) to represent the earthquake symbols on the map and html text to provide the labels. I won't get into the details of how this code works here, but look closely at it and see if you can figure out what's going on in each section. You'll be making legends of your own in the future, so it never hurts to begin understanding them now! 

```
<div id="legend">
		<h3><center>Earthquake magnitude</center></h3>
		<ul style="list-style-type:none">
			<li>
				<svg width="24" height="24">
					<circle cx="12" cy="20" r="2" stroke="red" stroke-width="3" fill="red" fill-opacity=".4"/>
				</svg>
				<text>Less than 1</text>
			</li>
			<li>
				<svg width="24" height="24">
					<circle cx="12" cy="18" r="4" stroke="red" stroke-width="3" fill="red" fill-opacity=".4"/>
				</svg>
				<text>Between 1 and 2.5</text>
			</li>
			<li>
				<svg width="24" height="24">
					<circle cx="12" cy="14" r="6" stroke="red" stroke-width="3" fill="red" fill-opacity=".4"/>
				</svg>
				<text>Between 2.5 and 4.5</text>
			</li>
			<li>
				<svg width="24" height="24">
					<circle cx="12" cy="12" r="10" stroke="red" stroke-width="3" fill="red" fill-opacity=".4"/>
				</svg>
				<text x="0" y="0)">Greater than 4.5</text>
			</li>
		</ul>
	</div>
```

Save your changes and view them in the web browser. If you scroll down, you should see the legend below the explanatory text. However, we want this to appear on the map itself. Toward the bottom of the `<body>`, just before the `</script>` tag closes, add the following JavaScript code:

```
	var Legend =  new L.Control.Legend({
		position: 'bottomright',
	});

	mymap.addControl(Legend);
	$(".legend-container").append( $("#legend") );
```

Now the legend should appear on the map itself. But it's hard to read. Let's add a little CSS styling to fix that. In the `<head>`, inside the `<style>` section of your code, add the following CSS:

```
	#legend {
		line-height: 0px;
		background: white;
		opacity: 0.8;
		padding:5px 25px 5px 0px;
	}
```

The final version of the map should look something like this!

<iframe src="https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex5.html" style="width:100%; height:500px;"></iframe> [View this example on its own](https://ejslgr.github.io/Leaflet-Intro/code-samples/earthquakes-ex5.html)
