# Lab 4: Geolocation and mobile optimization with Leaflet
## TGIS 504, Winter 2021, Dr. Emma Slager
### Introduction
In this lab, you will implement two aspects of JavaScript that can be hugely significant to web mapping: the [JavaScript Date object](https://www.w3schools.com/js/js_dates.asp) to work with time data, and and [the geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API/Using_the_Geolocation_API). You will create a map that places a marker on the map based on the locational information provided by the user's device, include signifiers that indicate to the user the accuracy of their device's locational information, add functionality that switches the base map between light and dark based on whether the sun is up at the user's location, and utilize various other design conventions. This lab also asks you to read relevant documentation on the technologies used and to answer a few questions about that documentation in order to assess your understanding of its contents.

Note: this lab is due in just 1 week and worth half as many points as a typical lab. That's because it is also about half the work of a typical lab.

The first part of this lab is based on the tutorial [Leaflet on Mobile](https://leafletjs.com/examples/mobile), with modifications and additions by myself.
### Set up your workspace
Begin by creating a project folder and create the necessary files, including an index.html, javascript.js, and styles.css file. Open the files in Atom or the text editor of your choice. Eventually, you will upload the files to GitHub, so you may wish to create a repository for your files now, which also provides the benefit of serving as a backup for your work. As always, I recommend saving your work frequently and testing it regularly using atom-live-server or a similar Atom package. We will not be using any geojson files in this lab, so you won't have cross-origin issues and local testing will therefore also be possible.

### Step 1: Prepare the page and initialize the map
In your index.html file, add the necessary links to Leaflet's CSS and JS libraries to the `head`. Per our discussion of the relative merits of CDNs versus locally hosted libraries, I recommend using a CDN in order to minimize bandwidth usage for best performance:
 ``` html
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.7.1/dist/leaflet.css"
   integrity="sha512-xodZBNTC5n17Xt2atTPuE1HxjVMSvLVW9ocqUKLsCC5CXdbqCmblAshOMAS6/keqq/sMZMZ19scR4PsZChSR7A=="
   crossorigin=""/>
   <script src="https://unpkg.com/leaflet@1.7.1/dist/leaflet.js"
   integrity="sha512-XQoYMqMTK8LvdxXYG3nZ448hOEQiglfqkJs1NOQV44cWnUrBc8PkAOcXy20w0vlaXaVUearIOBhiXZ5V3ynxwA=="
   crossorigin=""></script>
 ```
 In the `body` of the index, create a `div` element to hold the map:
  ``` html
  <div id="map"></div>
  ```
  Define the height and width of your map container in the CSS file and give the page some additional basic styling. In this lab, we want to maximize screen real estate for the map itself, so we'll set the height and width to 100%:
  ```css
  body {
    padding: 0;
    margin: 0;
  }

html, body, #map {
  height: 100%;
  width: 100vw;
}
  ```
Initialize the map in the JavaScript file, setting the map to display the whole world. We'll use Mapbox tiles for the basemap. Be sure to add your own personal Mapbox access token in the appropriate spot:
```javascript
var map = L.map('map').fitWorld();

L.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}', {
    attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
    maxZoom: 18,
    id: 'mapbox/streets-v11',
    accessToken: 'yourAccessToken',
}).addTo(map);
```
### Step 2: Geolocation
As we've discussed in lecture, we can access a device's location using the [Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API) that is built into JavaScript. Leaflet makes accessing location even easier with its built-in [`locate` method](https://leafletjs.com/reference-1.7.1.html#map-locate). We can call this method with a single line of code, and when we do so, we can set [various options](https://leafletjs.com/reference-1.7.1.html#locate-options), such as `setView`, which, if true, recenters the map on the user's location, or `watch` which, if true, will continuously watch the device's location instead of detecting it just once.

At the bottom of your JavaScript code, call the locate method with the following options:
```javascript
map.locate({setView: true, maxZoom: 16});
```
Here we set the maxZoom to 16, which preserves some spatial context even if a user's device gives location information that is precise enough that the setView option could zoom in further. Save your work and test the page. If everything is working as expected, the map should recenter and zoom to your location. Note that you need to give the webpage permission to access your location information before the map can access it.
#### A quick aside
You may notice that the text on your map is very difficult to read when zoomed in. This has something to do with retina screen detection errors and tile size (see more in a Stack Overflow thread [here](https://stackoverflow.com/questions/37040494/street-labels-in-mapbox-tile-layer-too-small)). If this happens for you, add the following options to your tile layer where you call it with the L.tileLayer method, below where you specify attribution, maxZoom, id, and accessToken:
```javascript
    tileSize: 512,
    zoomOffset: -1,
```
#### Now back to Geolocation

What if we want to do more than recenter and zoom the map after we've located the user's device? No sweat! Built into the `locate` method are two events that can fire after the method runs, `locationfound` and `locationerror`. We can write functions that will run if the location is found or if there is an error when the `locate` method tries to find the location (for instance, if the user doesn't give permission for the webpage to access their location).

Let's say we want to add a marker at the user's location if geolocation is successful and show an error message if geolocation fails. We can do so by writing functions and adding event listeners to our code. Note that the event listeners need to be included *before* the `map.locate` call! Add the following code before the `map.locate({setView: true, maxZoom: 16});` line. Read the comments to understand what the code is doing:

```javascript
function onLocationFound(e) {
    var radius = e.accuracy; //this defines a variable radius as the accuracy value returned by the locate method. The unit is meters.

    L.marker(e.latlng).addTo(map)  //this adds a marker at the lat and long returned by the locate function.
        .bindPopup("You are within " + Math.round(radius * 3.28084) + " feet of this point").openPopup(); //this binds a popup to the marker. The text of the popup is defined here as well. Note that we multiply the radius by 3.28084 to convert the radius from meters to feet and that we use Math.round to round the conversion to the nearest whole number.

    L.circle(e.latlng, radius).addTo(map); //this adds a circle to the map centered at the lat and long returned by the locate function. Its radius is set to the var radius defined above.
}

map.on('locationfound', onLocationFound); //this is the event listener
```
Next, we'll write a function that will run if the `locationerror` event fires.
```javascript
function onLocationError(e) {
  alert(e.message);
}

map.on('locationerror', onLocationError);

```
The message that displays will depend on the error; you can also find these errors in the JavaScript console in your web browser. I like to organize my code so that my functions are together and my event listeners are together right before the `map.locate` code, but you may organize things how you prefer.

At this point, you've basically completed the Leaflet on Mobile tutorial and your map should look something like their [complete example](https://leafletjs.com/examples/mobile/example.html) but with some custom improvements.

### Step 3: Giving the user feedback about the geolocation accuracy of their device
You've made some good progress on the lab. Well done. You've been doing your development and testing on a laptop or desktop computer; now, take a moment to test out your work on your mobile device. Upload your HTML, JS, and CSS files as a repository to GitHub and enable Pages for the repository. After your files have uploaded, visit the URL where they are hosted test things out, comparing what displays to what you see on your computer. In all likelihood, the radius of the circle you get with your mobile device is much smaller than the radius of the circle you get with your computer. This is because your mobile device has GPS, which provides much greater locational accuracy than the IP address the webpage accesses to geolocate your computer. The size of the circle is one form of **feedback** that our web map gives the user to understand the accuracy of their geolocation. But let's add a second form of feedback to make this even more clear to the user. We'll use conditional styling to change the color of the circle if the accuracy is above a certain level of accuracy--green if it's accurate within 100 meters and red if it's less accurate than that.

In the function `onLocationFound` replace the line of code that reads `L.circle(e.latlng, radius).addTo(map);` with the following:
```javascript
if (radius <= 100) {
      L.circle(e.latlng, radius, [color: 'green'}).addTo(map);
  }
  else{
      L.circle(e.latlng, radius, {color: 'red'}).addTo(mymap);
  }
```
Here we add styling to the circle instead of using the blue color that is the default in Leaflet. The style is set based on a conditional operator: **if** the circle's radius (which is determined by the accuracy reading returned by the locate method) is less than or equal to 100, the circle will be green. **Else** if the radius is greater than 100, the circle will be red. ***BEWARE*** Before you test this change out, note that I've included two small errors in the code block above that you need to correct before the code will function. Just trying to keep you on your toes!

### Step 4: Changing the basemap based on environmental conditions

As this week's readings noted, a significant constraint of mobile mapping is that environmental conditions can impact useability; thus, for instance, we tend to prefer high contrast color schemes to improve readability of maps in bright light, since many people use mobile maps outdoors and not just at climate controlled desktop computers. In the final step of creating a map that is optimized for mobile use, we're going to add functionality that switches between a light and dark themed base map, depending on the user's environmental condtions. If it's during daylight hours in the time and place that your user is viewing your map, the basemap will be Mapbox's light map, and if it's not during daylight hours, the basemap will be Mapbox's dark map.

First, we need to load tile layers for both the light and dark Mapbox styles. At the top of your JavaScript code, replace the lines where you add the `L.tileLayer` with the `id:'mapbox/streets-v11',` with the following code:
```javascript
var light = L.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}', {
    attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
    maxZoom: 18,
    id:'mapbox/light-v10',
    accessToken: 'yourAccessToken',
    tileSize: 512,
    zoomOffset: -1,
});

var dark = L.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}', {
    attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
    maxZoom: 18,
    id:'mapbox/dark-v10',
    accessToken: 'yourAccessToken',
    tileSize: 512,
    zoomOffset: -1,
});
```
We are replacing the single tile layer that used the Mapbox Streets style with two tile layers, one that uses the light style and one that uses the dark style. Note also that we have made each tile layer into a variable, which we have given the names `light` and `dark`, and that we have removed the method `addTo(map)` from the end of each section of code. 

Next, we need to initialize the map with one of the two tile layers. Let's choose light. Change the first line of the JavaScript code, where you initialize the map object, so that it reads as follows:

```javascript
var map = L.map('map', {layers:[light]}).fitWorld();
```
Then, move that line **below** the sections of code that add the two tile layers. Your map should load with the Mapbox Light style instead of the Streets style now.

But how do we get the basemap to change based on today's sunset and sunrise times in the user's location? Let's break this task down into component steps:
1. figure out what the user's current location is and what the current date is
2. figure out what time the sun rises and sets in the user's location on the current date
3. figure out what the current time is and determine if the sun is currently up or if it is set
4. style the basemap conditionally based on the sun's position

Believe it or not, this is pretty easy to do with JavaScript. To make it even easier, we're going to use a library called SunCalc that was developed by the same person who developed Leaflet, Vladimir Agafonkin.

Check out the [documentation for SunCalc](https://github.com/mourner/suncalc) and view a live example [here](http://suncalc.net/) (note that the basemap doesn't display correctly, but everything else on the example is functioning correctly). In the [Reference section](https://github.com/mourner/suncalc#reference) of the documentation, you'll see that if we use SunCalc's method `getTimes` and provide option parameters for the `date`, `latitude`, and `longitude`, the method will return information on sunrise time and sunset time, among other pieces of information. But how do we provide the parameters for date, lat, and lon based on the user's geolocation and the current date?

The date is the easiest to get, since JavaScript can provide this for us. We can simply create a Date object with `new Date()`. For more on this, see [this documentation](https://www.w3schools.com/jsref/jsref_obj_date.asp). The latitude and longitude are also fairly easy to get, since they are returned when Leaflet's `map.locate` method runs.

Inside the `onLocationFound` function in your JavaScript code, after the if/else statement that sets the color's circle but before the `}` that ends the function, add the following code:
```javascript
var times = SunCalc.getTimes(new Date(), e.latitude, e.longitude);
var sunrise = times.sunrise.getHours();
var sunset = times.sunset.getHours();

var currentTime = new Date().getHours();
    if (sunrise < currentTime && currentTime < sunset){
      map.removeLayer(dark);
      map.addLayer(light);
    }
    else {
      map.removeLayer(light);
      map.addLayer(dark);
    }
```
The first line of code (`var times`...) is using the SunCalc library to return all of the information about sunrise, sunset, dusk, dawn, solar noon, etc. for the date, latitude, and longitude parameters that are specified. We specify that the date parameter should be the current date by using JavaScript's `new Date()` constructor; we specify that latitude and longitude are the lat and lon returned by `map.locate` with `e.latitude` and `e.longitude` by placing this code inside the `onLocationFound(e)` function we wrote previously in Step 2.

Since `getTimes` returns a lot more information than we need, we pull out just the time of sunrise and sunset and declare them as variables in the next two lines of code. We use .getHours() to specify that we only want the hour of sunset and sunrise, not the full Hours, Minutes, and Seconds that SunCalc provides.

Next, we get the current time by again using the `new Date` constructor and specifying that we want just the current hour of the current date and time with `getHours()`. Finally, using an if/else conditional statement, we remove and add baselayers based on whether the current hour falls between sunrise and sunset.

Easy right? :)

But none of this will actually work if we don't also include a link to the SunCalc library in our index. If you've made the changes specified above and test your map, you won't see any changes, even if it's after sunset. If you open Developer Tools and view the JavaScript console, you'll see the following error: `Uncaught ReferenceError: SunCalc is not defined`. Search the web to find a SunCalc on a CDN, or follow these instructions to download the library from GitHub and host it locally:

From the [SunCalc Github page](https://github.com/mourner/suncalc), click the green 'Clone or download' button and download the ZIP. Extract just the `suncalc.js` file and save it in the same folder where the rest of your lab files are stored. Link to this file using a `<script>` tag in the `head` of your index.html file and test it again. Et voila!

### Step 5: Finishing touches

To complete your map, please do the following:
1. Create an alert window that displays on load or add explanatory text at the top of your webpage to explain that your page will ask the user for their location information. Tell them why it will do this and that you will not store or share their location information.
2. Add a layer control that allows the user to manually switch between light and dark basemaps, in case the automatically selected basemap isn't to their liking. You can do this without any additional Leaflet plugins. See [this tutorial](https://leafletjs.com/examples/layers-control/) for further help.
3. Set up the map.locate method to run at the click of a button rather than on page load. Because the user must give the browser permission to use their current location, it is a good idea to link the geolocation request to an action that the user must take (such as clicking a button), rather than having it run on page load, when the user is less likely to understand the purpose of the geolocation request and therefore more likely to deny permission. You can use an ordinary HTML button for this with a click event listener, or the [L.EasyButton plugin](https://github.com/CliffCloud/Leaflet.EasyButton) is another way to do this.

As always, reach out to me and one another for assistance as needed.

### Submission
Submit a link to your final work on Canvas along with brief answers to the following questions:
1. Regarding Leaflet's map.locate method (documentation [here](https://leafletjs.com/reference-1.7.1.html#locate-options)), if we don't specify any options when we call the method, will the map recenter if geolocation is found? (Hint: what is the default parameter for the setView option?)
2. Answers to the following questions about the Geolocation API documentation (https://www.w3.org/TR/geolocation-API/):

   a.	What is the confidence level on the accuracy value returned by the API?

   b.	In this lab, we haven't access data on the heading (or direction of travel) of the device, but using the Geolocation API, we could. If the Geolocation API returned a heading reading of 135, what direction would the device be facing?
3. Once you've uploaded your final map to GitHub Pages or your UW server space, test the map on at least one browser on your computer and at least one browser on your mobile device. List the browsers you ran your test with and briefly describe the differences in user experience between the devices/browsers. Did you run into any bugs while testing? If so, explain.
