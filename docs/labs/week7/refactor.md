# Extra Challenge: Refactoring

Do you think there's a better way for us to create the feature groups instead of adding all this code in our `formatData` function?

Yes, we should make a function to add each of the layers!

Due to lack of time, we won't do any refactoring, so for now we will leave the inefficient way of repeating ourselves with the various `featureGroups`. But we **must** **`refactor`** our code if we want to add our own buttons to interact with the map instead of using the default Leaflet `control` functions.

### Remember **refactoring**?

Refactoring code is means improving code so that it is easier to understand and easier to reuse. Refactoring is important because the less we repeat ourselves or hardcode things the less mistakes our code will have when we modify it.

## Steps to refactor the `group layers` code

To refactor the `group layers` code you want to do the following:

1. Create an array of `layers` by converting the `values` in the `layers` object to an array with `Object.values(layers)`

```js
// let layers = {
// 	"Speaks English First": englishFirst,
// 	"Doesn't Speak English First": nonEnglishFirst
// }

//this is the array of layers based on the layers object above
let layersArray = Object.values(layers); 

```

2. Create a new `function` that set layers to the map

```js
function setTheLayer(data){
    if (data['Is your English your first language?'] == "Yes"){
        return "Speaks English First"
    }
    else{
        return "Doesn't Speak English First"
    }
}
```

3. Call the function to set the layer inside of the loop for each of the data:

```js hl_lines="5"
function processData(results){
    console.log(results)
    results.data.forEach(data => {
        console.log(data)
        let layer = setTheLayer(data)
        addMarker(data)
    })
    let allLayers = L.featureGroup([englishFrist,nonEnglishFirst]);
    map.fitBounds(allLayers.getBounds());  
}

```

4. Have the `addMarker()` function use the `layer` set by the new function:

```js hl_lines="6"
function processData(results){
    console.log(results)
    results.data.forEach(data => {
        console.log(data)
        let layer = setTheLayer(data)
        addMarker(data,layer)
    })
    let allLayers = L.featureGroup([englishFrist,nonEnglishFirst]);
    map.fitBounds(allLayers.getBounds());  
}
```

5. Change the `addMarker()` function to use the `Layer` name to populate the pop-up title:
```js hl_lines="1 2"
function addMarker(data,layer){
    layers[layer].addLayer(L.marker([data.lat,data.lng]).bindPopup(`<h2>${layer}</h2>`))
    createButtons(data.lat,data.lng,data.Location)
    return data
}
```

6. Change `allLayers` to use `layersArray` and loop through the layers with a `forEach()` loop on the `layersArray` and add them to the map

```js hl_lines="8 9"
function processData(results){
    console.log(results)
    results.data.forEach(data => {
        console.log(data)
        let layer = setTheLayer(data)
        addMarker(data,layer)
    })
    let allLayers = L.featureGroup(layersArray);
    layersArray.forEach(layer => layer.addTo(map))
    map.fitBounds(allLayers.getBounds());  
}
```

Your refactored code should look like the following:

## Final Refactored Code

```js linenums="1" hl_lines="13 30-31 61-62 64-65 69-76" title="js/init.js"
// declare variables
let mapOptions = {'center': [34.0709,-118.444],'zoom':5}

let englishFirst = L.featureGroup();
let nonEnglishFirst = L.featureGroup();

let layers = {
	"Speaks English First": englishFirst,
	"Doesn't Speak English First": nonEnglishFirst
}

//this is the array of layers based on the layers object above
let layersArray = Object.values(layers); 

const dataUrl = "https://docs.google.com/spreadsheets/d/e/2PACX-1vS2WyfKTyZJ-_ja3GGrxoAXwranavyDGXYsxeFUO4nvHpCJrkKhChymXQqUEyhdGLnz9VN6BJv5tOjp/pub?gid=1560504149&single=true&output=csv"

// define the leaflet map
const map = L.map('the_map').setView(mapOptions.center, mapOptions.zoom);

// add layer control box
L.control.layers(null,layers).addTo(map)

let Esri_WorldGrayCanvas = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/Canvas/World_Light_Gray_Base/MapServer/tile/{z}/{y}/{x}', {
	attribution: 'Tiles &copy; Esri &mdash; Esri, DeLorme, NAVTEQ',
	maxZoom: 16
});

Esri_WorldGrayCanvas.addTo(map);

function addMarker(data,layer){
    layers[layer].addLayer(L.marker([data.lat,data.lng]).bindPopup(`<h2>${layer}</h2>`))
    createButtons(data.lat,data.lng,data.Location)
    return data
}

function createButtons(lat,lng,title){
    const newButton = document.createElement("button"); // adds a new button
    newButton.id = "button"+title; // gives the button a unique id
    newButton.innerHTML = title; // gives the button a title
    newButton.setAttribute("lat",lat); // sets the latitude 
    newButton.setAttribute("lng",lng); // sets the longitude 
    newButton.addEventListener('click', function(){
        map.flyTo([lat,lng]); //this is the flyTo from Leaflet
    })
    const spaceForButtons = document.getElementById('placeForButtons')
    spaceForButtons.appendChild(newButton);//this adds the button to our page.
}

function loadData(url){
    Papa.parse(url, {
        header: true,
        download: true,
        complete: results => processData(results)
    })
}

function processData(results){
    console.log(results)
    results.data.forEach(data => {
        console.log(data)
        let layer = setTheLayer(data)
        addMarker(data,layer)
    })
    let allLayers = L.featureGroup(layersArray);
    layersArray.forEach(layer => layer.addTo(map))
    map.fitBounds(allLayers.getBounds());  
}

function setTheLayer(data){
    if (data['Is your English your first language?'] == "Yes"){
        return "Speaks English First"
    }
    else{
        return "Doesn't Speak English First"
    }
}

loadData(dataUrl)
```