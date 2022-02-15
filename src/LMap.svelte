<script>
import leg22 from './data/house2022.json';
import leg12 from './data/house2012.json';
import precincts from './data/potus-results-geo.json';
import { onMount } from 'svelte';
import * as jq from 'jquery';
import * as mapboxgl from 'mapbox-gl';
import * as MapboxGeocoder from '@mapbox/mapbox-gl-geocoder';
import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';
import * as turf from '@turf/turf';

/********** MAP CONFIG VARIABLES **********/
let center = [-94.351646, 46.607469]; //default mobile centerpoint
let mcenter = [-93.907810, 45.940497]; //default mobile centerpoint
let metrocenter = [-93.218950, 44.935852]; //default metro area centerpoint
let zoom = 5.5; //default desktop zoom
let mzoom = 5.2; //default mobile zoom level
let metrozoom = 9; //default metro area zoom level
let condition = 'mousemove';
var mclick = false;

function makeMap() {
/********** INITIALIZE MAP **********/
const map2 = new mapboxgl.Map({
  container: 'mapL',
  style: 'mapbox://styles/startribune/ck1b7427307bv1dsaq4f8aa5h',
  center: center,
  zoom: zoom,
  minZoom: 5.2,
  maxZoom: 14,
  maxBounds: [-107.2,40.88,-78.92,51.62],
  scrollZoom: false
});

/********** GEOCODER CONFIGURATION **********/
const geocoder2 = new MapboxGeocoder({
      accessToken: mapboxgl.accessToken,     
      marker: { color: '#5bbf48' },
      countries: 'us',
      bbox: [-97.24,43.5,-89.48,49.38],
      proxmity: center,
      placeholder: 'Search for an address or location name...',
      zoom: 13,
      mapboxgl: mapboxgl
    });

document.getElementById('geocoder2').appendChild(geocoder2.onAdd(map2));

/********** GEOCODER DISTRICT LOCATION RETURN **********/
  var geoPoint;
  var oldDistrict;
  var newDistrict;

  function searchWithin(layer, point) {
    var polygon;
    for (var i=0; i < layer.features.length; i++) {
      polygon = turf.multiPolygon(layer.features[i].geometry.coordinates);
      var test = turf.booleanPointInPolygon(point, polygon);
      if (test == true) { return layer.features[i].properties.DISTRICT; }
    }
    return 'X';
  }

  geocoder2.on('result', (event) => {
    if (event.result.center[0] != null) {
      geoPoint = [event.result.center[0], event.result.center[1]];
      oldDistrict = searchWithin(leg12, geoPoint);
      newDistrict = searchWithin(leg22, geoPoint);
      if (oldDistrict != "X") { 
        jq("#resultL").html('That location was centered within District <span class="nowD">' + oldDistrict + '</span>, and is now in <span class="newH">District <span class="newD">' + newDistrict + '</span></span>.')
        jq("#resultL").css('visibility','visible');
      } else {
        jq("#resultL").html("Location not found within Minnesota's districts.");
        jq("#resultL").css('visibility','visible');
      }
    }
  });
  geocoder2.on('clear', (event) => {
    jq("#resultL").css('visibility','hidden');
  });

/********** SPECIAL STATE AND METRO RESET BUTTONS **********/
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
    el.innerHTML = '<img width="15" src="https://static.startribune.com/news/projects/all/20220215-redistrict/build/img/mn.png" alt="mn" />';
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
    el.innerHTML = '<i style="font-size:20px" class="fas">&#xf1ad;</i>';
    el.addEventListener('click',(e)=>{
     // e.style.display = 'none'
     // e.stopPropagation()
    },false )
    return el;
  }
}
const toggleControlM = new MetroReset();


/********** SETUP BASIC MAP CONTROLS FOR DESKTOP AND MOBILE **********/
var scale = new mapboxgl.ScaleControl({
  maxWidth: 80,
  unit: 'imperial'
  });

if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
  //map2.dragPan.disable();
  map2.keyboard.disable();
  map2.dragRotate.disable();
  map2.touchZoomRotate.disableRotation();
  map2.scrollZoom.disable();
  map2.addControl(new mapboxgl.NavigationControl({ showCompass: false }),'bottom-left');
  map2.addControl(toggleControl,'bottom-left');
  map2.addControl(toggleControlM,'bottom-left');
  condition = 'click';
  mclick = true;
} else {
  map2.addControl(scale);
  map2.getCanvas().style.cursor = 'pointer';
  map2.addControl(new mapboxgl.NavigationControl({ showCompass: false }),'bottom-left');
  map2.addControl(toggleControl,'bottom-left');
  map2.addControl(toggleControlM,'bottom-left');
}

jq('#mapL .statereset').on('click', function(){
  if ((jq("#map").width() < 520)) { 
    zoom = 5.2;
  } else { zoom = 5.5; }
  map2.jumpTo({
    center: center,
    zoom: zoom,
  });
});

jq('#mapL .metroreset').on('click', function(){
  map2.jumpTo({
    center: metrocenter,
    zoom: metrozoom,
  });
});

/********** ADD MAP LAYERS **********/
map2.on('load', function() {

      map2.addSource('precincts', {
        type: 'geojson',
        data: precincts
      });

      map2.addLayer({
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
                      0.80
                  ]
            }
        }, "settlement-subdivision-label");

      map2.addSource('leg12', {
        type: 'geojson',
        data: leg12,
        generateId: true
      });

      map2.addLayer({
            'id': 'leg12',
            'interactive': true,
            'source': 'leg12',
            'layout': {},
            'type': 'fill',
            'paint': {
              'fill-color': '#000000',
              'fill-opacity': ['case', ['boolean', ['feature-state', 'hover'], false], 0.05, 0],
              'fill-outline-color': '#ffffff'
            }
        }, "settlement-subdivision-label");

      map2.addLayer({
            'id': 'leg12-l',
            'interactive': true,
            'source': 'leg12',
            'layout': {},
            'type': 'line',
            'paint': {
              'line-width': 0.5,
              'line-color': '#555555'
            }
        }, "settlement-subdivision-label");

      map2.addSource('leg22', {
        type: 'geojson',
        data: leg22,
        generateId: true
      });

      map2.addLayer({
            'id': 'leg22',
            'interactive': true,
            'source': 'leg22',
            'layout': {},
            'type': 'fill',
            'paint': {
              'fill-color': '#000000',
              'fill-opacity': ['case', ['boolean', ['feature-state', 'hover'], false], 0.05, 0],
              'fill-outline-color': '#ffffff'
            }
        }, "settlement-subdivision-label");

        map2.addLayer({
            'id': 'leg22-l',
            'interactive': true,
            'source': 'leg22',
            'layout': {},
            'type': 'line',
            'paint': {
              'line-width': 0.5,
              'line-color': '#000000'
            }
        }, "settlement-subdivision-label");

        map2.setPaintProperty('leg12-l', 'line-width', ['interpolate',['exponential', 0.5], ['zoom'],5,0.5,13,1.5]);
        map2.setPaintProperty('leg22-l', 'line-width', ['interpolate',['exponential', 0.5], ['zoom'],5,0.5,13,1.5]);

        map2.setLayoutProperty('leg12', 'visibility', 'none');
        map2.setLayoutProperty('leg12-l', 'visibility', 'none');

/********** TOOLTIP AND HOVER EFFECTS **********/
        let hoveredStateId = null;

        function tooltips(layer) {
          const popup2 = new mapboxgl.Popup({
          closeButton: mclick,
          closeOnClick: mclick
          });
          
          var when = "Formerly";
          if (layer == 'leg22') { when = "Now"; }  

          map2.on(condition, layer, function(e) {
                map2.getCanvas().style.cursor = 'default';
                var feature = e.features[0];

                if (e.features.length > 0) {
                    if (hoveredStateId !== null) {
                      map2.setFeatureState({ source: layer, id: hoveredStateId },{ hover: false });
                    }
                    hoveredStateId = feature.id;
                      map2.setFeatureState({ source: layer, id: hoveredStateId },{ hover: true });
                }

                popup2.setLngLat(e.lngLat)
                    .setText(when + ": MN House District " + feature.properties.DISTRICT)
                    .addTo(map2);
            });

            map2.on('mouseleave', layer, function() {
                map2.getCanvas().style.cursor = '';
                popup2.remove();
                if (hoveredStateId !== null) {
                  map2.setFeatureState({ source: layer, id: hoveredStateId },{ hover: false });
                }
                hoveredStateId = null;
            });
    }
    tooltips('leg12');
    tooltips('leg22');
});

/********** MOBILE ZOOM ADJUSTMENTS **********/
jq(document).ready(function() {
  map2.resize();
  var cachedWidth = jq(window).width();
  if ((jq("#map").width() < 520)) {
      center = mcenter;
      map2.flyTo({
          center: mcenter,
          zoom: mzoom
      });
  }
  jq(window).resize(function() {
    var newWidth = jq(window).width();
    if(newWidth !== cachedWidth){
      cachedWidth = newWidth;
      if ((jq("#map").width() < 520)){
          center = mcenter;
          map2.flyTo({
              center: mcenter,
              zoom: mzoom
          });
      } else {
          center = [-94.351646, 46.607469];
          map2.flyTo({
              center: center,
              zoom: zoom
          });
      }
    }
  });
});

/********** MAP LAYER TOGGLES **********/
jq("#legSwitch").change(function() {
    if(this.checked) {
        map2.setLayoutProperty('leg12', 'visibility', 'none');
        map2.setLayoutProperty('leg12-l', 'visibility', 'none');
        map2.setLayoutProperty('leg22', 'visibility', 'visible');
        map2.setLayoutProperty('leg22-l', 'visibility', 'visible');
        jq("#miniL .current").hide();
        jq("#miniL .new").show();
    } else {
        map2.setLayoutProperty('leg12', 'visibility', 'visible');
        map2.setLayoutProperty('leg12-l', 'visibility', 'visible');
        map2.setLayoutProperty('leg22', 'visibility', 'none');
        map2.setLayoutProperty('leg22-l', 'visibility', 'none');
        jq("#miniL .new").hide();
        jq("#miniL .current").show();
    }
});
}

    onMount(() => {
        makeMap();
    });
</script>
<div id="geocoder2" class="geocoder"></div>

<div class="results" id="resultL">That location was centered within District <span class="nowD">X</span>, and is now in District <span class="newH">District <span class="newD">X</span></span>.</div>

<div class="map" id="mapL">
      <div class="switcher">
      <div class="instructions">Districts</div>
      <div class="toggle">
        <span class="tlabel">&larr; OLD</span> 
        <label class="switch">
          <input id="legSwitch" type="checkbox" checked>
          <span class="slider"></span>
        </label>
        <span class="tlabel">NEW &rarr;</span>
      </div>
      </div>

      <div class="mlegend">
            <div><span>&nbsp;</span><span style="text-align:right;">&nbsp;</span><span style="text-align:right;">D</span><span>&nbsp;</span><span>R</span><span>&nbsp;</span><span>&nbsp;</span></div>
            <div class="strong"><span style="background-color: #5e758b"></span><span style="background-color: #8697a9"></span><span style="background-color: #DAE1E7"></span><span style="background-color: #ccc900"></span><span style="background-color: #f3d1c9"></span><span style="background-color: #d5837c"></span><span style="background-color: #9b4242"></span></div>
        </div>

      <div class="legend">
        <strong>2020 presidential results</strong>
        <div><span>&nbsp;</span><span style="text-align:right;">&larr;</span><span style="text-align:right;">D</span><span>&nbsp;</span><span>R</span><span>&rarr;</span><span>&nbsp;</span></div>
        <div class="strong"><span style="background-color: #5e758b"></span><span style="background-color: #8697a9"></span><span style="background-color: #DAE1E7"></span><span style="background-color: #ccc900"></span><span style="background-color: #f3d1c9"></span><span style="background-color: #d5837c"></span><span style="background-color: #9b4242"></span> &darr; votes</div>
        <div class="middle"><span style="background-color: #5e758b"></span><span style="background-color: #8697a9"></span><span style="background-color: #DAE1E7"></span><span style="background-color: #ccc900"></span><span style="background-color: #f3d1c9"></span><span style="background-color: #d5837c"></span><span style="background-color: #9b4242"></span></div>
        <div class="weak"><span style="background-color: #5e758b"></span><span style="background-color: #8697a9"></span><span style="background-color: #DAE1E7"></span><span style="background-color: #ccc900"></span><span style="background-color: #f3d1c9"></span><span style="background-color: #d5837c"></span><span style="background-color: #9b4242"></span></div>
      </div>
</div>