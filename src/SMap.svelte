<script>
import sen22 from './data/senate2022.json';
import sen12 from './data/senate2012.json';
import precincts from './data/potus-results-geo.json';
import { onMount } from 'svelte';
import * as jq from 'jquery';
import * as mapboxgl from 'mapbox-gl';
import * as MapboxGeocoder from '@mapbox/mapbox-gl-geocoder';
import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';
import * as turf from '@turf/turf';

let center = [-94.351646, 46.607469];
let mcenter = [-94.022056, 46.622562];
let metrocenter = [-93.218950, 44.935852]
let zoom = 5.5;
let mzoom = 5.2;
let metrozoom = 9;

mapboxgl.accessToken = 'pk.eyJ1Ijoic3RhcnRyaWJ1bmUiLCJhIjoiY2sxYjRnNjdqMGtjOTNjcGY1cHJmZDBoMiJ9.St9lE8qlWR5jIjkPYd3Wqw';

function makeMap() {
/********** MAKE MAP **********/

const map3 = new mapboxgl.Map({
  container: 'mapS',
  style: 'mapbox://styles/startribune/ck1b7427307bv1dsaq4f8aa5h',
  center: center,
  zoom: zoom,
  minZoom: 5,
  maxZoom: 13,
  maxBounds: [-107.2,40.88,-78.92,51.62],
  scrollZoom: false
});

//geocoder
const geocoder3 = new MapboxGeocoder({
      accessToken: mapboxgl.accessToken,     
      marker: { color: '#5bbf48' },
      countries: 'us',
      bbox: [-97.24,43.5,-89.48,49.38],
      placeholder: 'Search for an address...',
      zoom: 13,
      mapboxgl: mapboxgl
    });

document.getElementById('geocoder3').appendChild(geocoder3.onAdd(map3));

  var geoPoint;
  var oldDistrict;
  var newDistrict;

  function searchWithin(layer, point) {
    var polygon;
    console.log(layer);
    for (var i=0; i < layer.features.length; i++) {
      polygon = turf.multiPolygon(layer.features[i].geometry.coordinates);
      var test = turf.booleanPointInPolygon(point, polygon);
      if (test == true) { return layer.features[i].properties.DISTRICT; }
    }
  }

  geocoder3.on('result', (event) => {
    if (event.result.center[0] != null) {
      geoPoint = [event.result.center[0], event.result.center[1]];
      oldDistrict = searchWithin(sen12, geoPoint);
      newDistrict = searchWithin(sen22, geoPoint);
      jq("#resultS .nowD").text(oldDistrict);
      jq("#resultS .newD").text(newDistrict);
      jq("#resultS").css('visibility','visible');
    } else {
      //NOT FOUND
    }
  });
  geocoder3.on('clear', (event) => {
    jq("#resultS").css('visibility','hidden');
  });
/********** SPECIAL RESET BUTTON **********/
class HomeReset {
  onAdd(map){
    this.map = map;
    this.container = document.createElement('div');
    this.container.className = 'mapboxgl-ctrl my-custom-control mapboxgl-ctrl-group statereset';

    const button = this._createButton('mapboxgl-ctrl-icon StateFace monitor_button')
    this.container.appendChild(button);
    return this.container;
  }
  onRemove(){
    this.container.parentNode.removeChild(this.container);
    this.map = undefined;
  }
  _createButton(className) {
    const el = window.document.createElement('button')
    el.className = className;
    el.innerHTML = '<img width="15" src="./img/mn.png" alt="mn" />';
    el.addEventListener('click',(e)=>{
     // e.style.display = 'none'
     // e.stopPropagation()
    },false )
    return el;
  }
}
const toggleControl = new HomeReset();

class MetroReset {
  onAdd(map){
    this.map = map;
    this.container = document.createElement('div');
    this.container.className = 'mapboxgl-ctrl my-custom-control mapboxgl-ctrl-group metroreset';

    const button = this._createButton('mapboxgl-ctrl-icon StateFace monitor_button')
    this.container.appendChild(button);
    return this.container;
  }
  onRemove(){
    this.container.parentNode.removeChild(this.container);
    this.map = undefined;
  }
  _createButton(className) {
    const el = window.document.createElement('button')
    el.className = className;
    el.innerHTML = '<img width="15" src="./img/metro.png" alt="mn" />';
    el.addEventListener('click',(e)=>{
     // e.style.display = 'none'
     // e.stopPropagation()
    },false )
    return el;
  }
}
const toggleControlM = new MetroReset();

var scale = new mapboxgl.ScaleControl({
  maxWidth: 80,
  unit: 'imperial'
  });

// Setup basic map controls
if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
  map3.dragPan.disable();
  map3.keyboard.disable();
  map3.dragRotate.disable();
  map3.touchZoomRotate.disableRotation();
  map3.scrollZoom.disable();
  jq("#map").css("pointer-events","none");
  map3.addControl(new mapboxgl.NavigationControl({ showCompass: false }),'bottom-left');
  map3.addControl(toggleControl,'bottom-left');
  map3.addControl(toggleControlM,'bottom-left');
} else {
  map3.addControl(scale);
  map3.getCanvas().style.cursor = 'pointer';
  map3.addControl(new mapboxgl.NavigationControl({ showCompass: false }),'bottom-left');
  map3.addControl(toggleControl,'bottom-left');
  map3.addControl(toggleControlM,'bottom-left');
}

jq('#mapS .my-custom-control').on('click', function(){
  map3.jumpTo({
    center: center,
    zoom: zoom,
  });
});

jq('#mapS .metroreset').on('click', function(){
  map3.jumpTo({
    center: metrocenter,
    zoom: metrozoom,
  });
});

/********** MAP BEHAVIORS **********/

map3.on('load', function() {

      map3.addSource('precincts', {
        type: 'geojson',
        data: precincts
      });

      map3.addLayer({
            'id': 'precincts-layer',
            'interactive': true,
            'source': 'precincts',
            'layout': {},
            'type': 'fill',
            'paint': {
              'fill-color': [
                  'interpolate',
                  ['linear'],
                  ['get', 'wmargin'],
                    -80,
                    '#9b4242',
                    -60,
                    '#b9625f',
                    -40,
                    '#d5837c',
                    -20,
                    '#eaa99e',
                    -10,
                    '#f3d1c9',
                    -0,
                    '#ccc900',
                     10,
                    '#DAE1E7',
                     20,
                    '#b0bcc8',
                     40,
                    '#8697a9',
                     60,
                    '#5e758b',
                     80,
                    '#36546f',
                ],
                'fill-opacity': [
                  'interpolate',
                  ['linear'],
                  ['get', 'votes_sqmi'],
                      0,
                      0.10,
                      20,
                      0.20,
                      30,
                      0.30,
                      40,
                      0.40,
                      50,
                      0.50,
                      60,
                      0.60,
                      70,
                      0.70,
                      80,
                      0.80,
                      90,
                      0.90,
                      100,
                      1
                  ]
            }
        }, "settlement-subdivision-label");

      map3.addSource('sen12', {
        type: 'geojson',
        data: sen12,
        generateId: true
      });

      map3.addLayer({
            'id': 'sen12',
            'interactive': true,
            'source': 'sen12',
            'layout': {},
            'type': 'fill',
            'paint': {
              'fill-color': '#000000',
              'fill-opacity': ['case', ['boolean', ['feature-state', 'hover'], false], 0.05, 0],
              'fill-outline-color': '#ffffff'
            }
        }, "settlement-subdivision-label");

      map3.addLayer({
            'id': 'sen12-l',
            'interactive': true,
            'source': 'sen12',
            'layout': {},
            'type': 'line',
            'paint': {
              'line-width': 0.5,
              'line-color': '#333333'
            }
        }, "settlement-subdivision-label");

      map3.addSource('sen22', {
        type: 'geojson',
        data: sen22,
        generateId: true
      });

      map3.addLayer({
            'id': 'sen22',
            'interactive': true,
            'source': 'sen22',
            'layout': {},
            'type': 'fill',
            'paint': {
              'fill-color': '#000000',
              'fill-opacity': ['case', ['boolean', ['feature-state', 'hover'], false], 0.05, 0],
              'fill-outline-color': '#ffffff'
            }
        }, "settlement-subdivision-label");

      map3.addLayer({
            'id': 'sen22-l',
            'interactive': true,
            'source': 'sen22',
            'layout': {},
            'type': 'line',
            'paint': {
              'line-width': 0.5,
              'line-color': '#333333'
            }
        }, "settlement-subdivision-label");

        map3.setPaintProperty('sen12-l', 'line-width', ['interpolate',['exponential', 0.5], ['zoom'],5,0.5,13,1.5]);
        map3.setPaintProperty('sen22-l', 'line-width', ['interpolate',['exponential', 0.5], ['zoom'],5,0.5,13,1.5]);

        map3.setLayoutProperty('sen22', 'visibility', 'none');
        map3.setLayoutProperty('sen22-l', 'visibility', 'none');

        let hoveredStateId = null;

        function tooltips(layer) {
          const popup3 = new mapboxgl.Popup({
          closeButton: false,
          closeOnClick: false
          });
          
          map3.on('mousemove', layer, function(e) {
                map3.getCanvas().style.cursor = 'pointer';
                var feature = e.features[0];

                if (e.features.length > 0) {
                    if (hoveredStateId !== null) {
                      map3.setFeatureState({ source: layer, id: hoveredStateId },{ hover: false });
                    }
                    hoveredStateId = feature.id;
                      map3.setFeatureState({ source: layer, id: hoveredStateId },{ hover: true });
                }

                popup3.setLngLat(e.lngLat)
                    .setText("MN Senate District " + feature.properties.DISTRICT)
                    .addTo(map3);
            });

            map3.on('mouseleave', layer, function() {
                map3.getCanvas().style.cursor = '';
                popup3.remove();
                if (hoveredStateId !== null) {
                  map3.setFeatureState({ source: layer, id: hoveredStateId },{ hover: false });
                }
                hoveredStateId = null;
            });
    }
    tooltips('sen12');
    tooltips('sen22');
});

//MAP LAYER TOGGLE
jq("#senSwitch").change(function() {
  console.log("checked");
    if(this.checked) {
        map3.setLayoutProperty('sen12', 'visibility', 'none');
        map3.setLayoutProperty('sen12-l', 'visibility', 'none');
        map3.setLayoutProperty('sen22', 'visibility', 'visible');
        map3.setLayoutProperty('sen22-l', 'visibility', 'visible');
        jq("#miniS .current").hide();
        jq("#miniS .new").show();
    } else {
        map3.setLayoutProperty('sen12', 'visibility', 'visible');
        map3.setLayoutProperty('sen12-l', 'visibility', 'visible');
        map3.setLayoutProperty('sen22', 'visibility', 'none');
        map3.setLayoutProperty('sen22-l', 'visibility', 'none');
        jq("#miniS .new").hide();
        jq("#miniS .current").show();
    }
});

jq(document).ready(function() {
  if ((jq("#map").width() < 520)) {
      map3.flyTo({
          center: mcenter,
          zoom: mzoom
      });
  }
  jq(window).resize(function() {
      if ((jq("#map").width() < 520)){
          map3.flyTo({
              center: mcenter,
              zoom: mzoom
          });
      } else {
          map3.flyTo({
              center: center,
              zoom: zoom
          });
      }
  });
});
}

    onMount(() => {
        makeMap();
    });
</script>

<div class="switcher">
<div class="instructions">toggle between borders</div>
<div class="toggle">
  <span class="tlabel">&larr; OLD</span> 
  <label class="switch">
    <input id="senSwitch" type="checkbox">
    <span class="slider"></span>
  </label>
  <span class="tlabel">NEW &rarr;</span>
</div>
</div>

<div id="geocoder3" class="geocoder"></div>

<div class="results" id="resultS">That location was within District <span class="nowD">X</span>, and will now be in District <span class="newD">X</span>.</div>

<div class="map" id="mapS">
      <div class="legend">
        <strong>2020 presidential results</strong>
        <div><span>&nbsp;</span><span style="text-align:right;">&larr;</span><span style="text-align:right;">D</span><span>&nbsp;</span><span>R</span><span>&rarr;</span><span>&nbsp;</span></div>
        <div class="strong"><span style="background-color: #5e758b"></span><span style="background-color: #8697a9"></span><span style="background-color: #DAE1E7"></span><span style="background-color: #ccc900"></span><span style="background-color: #f3d1c9"></span><span style="background-color: #d5837c"></span><span style="background-color: #9b4242"></span></div>
        <div class="middle"><span style="background-color: #5e758b"></span><span style="background-color: #8697a9"></span><span style="background-color: #DAE1E7"></span><span style="background-color: #ccc900"></span><span style="background-color: #f3d1c9"></span><span style="background-color: #d5837c"></span><span style="background-color: #9b4242"></span></div>
        <div class="weak"><span style="background-color: #5e758b"></span><span style="background-color: #8697a9"></span><span style="background-color: #DAE1E7"></span><span style="background-color: #ccc900"></span><span style="background-color: #f3d1c9"></span><span style="background-color: #d5837c"></span><span style="background-color: #9b4242"></span> &uarr; votes</div>
      </div>
</div>