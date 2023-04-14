# Extra: Creating a GeoJSON file

Our class projects will connect information from our surveys to our map will, so first we will practice by creating a GeoJSON file of our own!

## The power of people-based web mapping

Let's put to practice what web development and GIS can do for empowering our own stories.

Head over to [GeoJSON.io](http://www.geojson.io/):

- [http://www.geojson.io/](http://www.geojson.io/)

Click on the ==marker :material-map-marker:== tool:

![](../week3/media/geojson1.png)

Click on a location of interest to you:

![](../week3/media/geojson2.png)

Switch to the Table view by clicking on ==:material-table: Table==:

![](../week3/media/geojson_table.png)

Add a data column by clicking on ==:heavy_plus_sign: new column== :

![](../week3/media/geojson3.png)

Call it ==place== and click `OK`:

![](../week3/media/geojson4.png)

Click inside the ==`place`== column

![](../week3/media/geojson6.png)

Type in a description for the `place`, in this case I called it ==home==.

![](../week3/media/geojson_home.png){: style="max-width:150px"}

Zoom out by pressing the ==:heavy_minus_sign:== button or ++ minus ++ key:

![](../week3/media/geojson8.png)

Click the ==edit :material-square-edit-outline:== button:

![](../week3/media/geojson9.png)

Click on the marker and move it the adjust the location:

![](../week3/media/geojson9a.png)

Click the  edit :material-square-edit-outline: button and then ==Save== to save your edits:

![](../week3/media/geojson10.png)


Add a new column called ==color==, to put some color to your map later.

![](../week3/media/geojson7a.png)

When you are done, save your file by going to the top menu's ==Save== option:

![](../week3/media/geojson11.png)

Click ==GeoJSON==:

![](../week3/media/geojson12.png)

Download the file to your computer:

![](../week3/media/geojson13.png){: style="max-width:300px"}

Copy the file into your project folder:

![](../week3/media/geojson15.png)

### ⚽In-class Exercise #1 - Leaving  your mark(er) on the map!

Go back and add more points to your GeoJSON file.

!!! tldr "Tasks"
    1. Add some points into your GeoJSON file
    2. Save the file and add it to your lab3 folder

After finishing the exercise, think about how empowering it was for you to be able to add data to the map yourselves. Whether you were clicking random spots or trying to find your old favorite places to visit, the ability to mark things is a reclaiming of mapping for yourself. This sense of staking a claim is what is meant when we refer to "empowering community voices".

## 🏁Checkpoint - Add your GeoJSON to your map

1. Make sure your GeoJSON file is in your `week2` folder!

2. Take note of the filename!

3. Modify your `js/init.js` file to add the GeoJSON file to your map:

```js title="js/init.js" linenums="1" hl_lines="2 5 18-25"
// declare variables
let mapOptions = {'center': [34.0709,-118.444],'zoom':5}

// use the variables
const map = L.map('the_map').setView(mapOptions.center, mapOptions.zoom);

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
}).addTo(map);

// create a function to add markers
function addMarker(lat,lng,title,message){
    console.log(message)
    L.marker([lat,lng]).addTo(map).bindPopup(`<h2>${title}</h2> <h3>${message}</h3>`)
    return message
}

fetch("map.geojson") // fetch the GeoJSON file, this is the name from step 2.
    .then(response => {
        return response.json();
    })
    .then(data =>{
        // Basic Leaflet method to add GeoJSON data
        L.geoJSON(data).addTo(map)
    });
```
