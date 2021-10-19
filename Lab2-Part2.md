# Lab 2 Part 2: Leaflet with custom data

### Introduction
This lab is an introduction to adding real datasets to an interactive web map built with the Leaflet web mapping service. For this lab, you will choose a topic (some natural or human-created phenomenon) and map static data to teach about that topic. You should use a minimum of two data sets, one that is a point layer and one that is a polygon or line layer. The data sets should be related to one another and to your topic. For example, many volcanoes are close to human settlements and present risks to people and places in their vicinities. On this topic, you could make a map that depicts the polygons that cover the anticipated path of lahar flows that would be produced by volcanic eruptions in Washington and a point layer of state-recognized historical sites that would be destroyed by the lahars. 

### Background Details
In previous labs & coursework you have learned to embed simple web maps in a webpage for users to navigate. With that baseline knowledge, we will begin to delve into web GIS. Our first task is to take static datasets and display them on your webpage. 

As you continue to practice and improve your web mapping skills, we will begin to require more appealing and persuasive maps. The Leaflet mapping framework allows for much more stylization and functionality than Google My Maps, and therefore it is expected that any maps produced for this lab are styled beyond the default user interface. For example, point icons might be changed to custom icons to more intuitively depict the phenomena being mapped; polygon colors/transparency should be selected to make the map more readable; or basemap tiles might be chosen to improve figure-ground. Additionally, you will use at least one Leaflet plug-in to add greater functionality to your map. 

As with many projects, data collection will be a potential bottleneck in this lab. As a general rule, government datasets and open data portals are an ideal starting point for finding spatial data. 

### Learning Outcomes
This lab will give students practice with:
* The basics of adding web-maps to HTML pages using Leaflet
* Conventional data formats for web mapping, specifically GeoJSON
* Preparing a dataset for display in a web map by converting or simplifying as necessary
* Displaying static data (points and polygons/lines) on Leaflet maps
* Displaying multiple data sources or maps in the same web-page using different HTML element IDs
* Critically considering color/style choices to convey the desired message from their maps
* Incorporating Leaflet plug-ins with JavaScript 

### Expectations & Technical Specifications
For this lab you should create a single HTML page with one or more maps embedded in it, and you should utilize at least one Leaflet Plugin. The page should have details about the phenomenon you are depicting and should include at least two data layers to help convey your message. At least one layer should have clickable pop-ups to display additional information about the layer. Also, you should specify the source(s) of your data sets and describe any transformations, simplifications, or format conversions you performed on your data (more about these below). 

#### Data format 
In class we learned about displaying GeoJSON datasets, I expect you to stick with this unless you have a compelling reason not to, which you must discuss with me in advance. My recommendation is that you use data that you can download already formatted in GeoJSON, but if your data is in a different format (like a shapefile), you may find an online tool to convert the data set into GeoJSON. We will cover data conversions and other transformations in more detail in Lab 3.

#### Plugins
You already have some experience with plugins from Lab 2, Part 1 (specifically, the leaflet-legend plugin: https://github.com/zostera/leaflet-legend). You can browse the list of available plugins and find documentation and tutorials for them here: https://leafletjs.com/plugins.html. I expect you to incorporate at least one plugin in your Lab 2, Part 2 submission. This may be the leaflet-legend plugin again, or it could be a plugin to achieve something else, such as easy custom icons, an interactive north arrow, full-screen capability, a layer control, or something else that makes sense for your map. 

#### Minimum Requirements
This lab must:
* Be submitted as a URL link to a web page
* Be contained on a single HTML page, with a link to an external style sheet for your page’s styling
* Have a title & accompanying text to describe the phenomenon being mapped
* Have a map that is stylized to encourage consumption of the data and understanding of the map (not just generic markers & basemaps)
* Zoom and set map extent to an appropriate location for displaying the data
* Have clickable points or polygons that display some detail about the data (ex: clicking a point reveals the name of that point)
* Include references for all data you did not produce yourself, including basemap imagery, GeoJSON overlays, custom icons, etc. 
* Include at least one Leaflet plugin that enhances the map in some way

#### Bonus Requirements
Bonus points may be awarded on a case-by-case basis for things like: 
* Exceptional incorporation of plugin(s)
* Exceptional design
* Incorporation of additional data sets that enrich your map’s exploration of your topic
