# PubNub and MapBox

Subscribe and Publish to a Pubnub channel & visualize it on a Mapbox Map


## Publish geolocation to channel

Include all dependencies and create a div to hold the map in place.

```html
<!doctype html>
<html class="no-js">
  <head>
    <meta charset="utf-8">
    <title>MapBox Pubnub Example</title>
    <meta name="description" content="">
    <meta name="viewport" content="initial-scale=1.0, user-scalable=no">
    <script type="text/javascript" src="https://pubnub.github.io/eon/v/eon/1.0.0/eon.js"></script>
    <link type="text/css" rel="stylesheet" href="https://pubnub.github.io/eon/v/eon/1.0.0/eon.css"/>
    <script src="https://cdn.pubnub.com/pubnub-3.7.1.min.js"></script>
      <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.1/css/bootstrap.min.css">
        <script src='https://api.tiles.mapbox.com/mapbox.js/v2.1.4/mapbox.js'></script>
<div id='map' style="height:  500px;"></div>

  </body>
</html>



```


Get the geolocation of the browser and publish the position coordinates to the pubnub channel every 10000 ms


Have a look at the [Pubnub Console](https://www.pubnub.com/docs/console) to see the datastream in realtime.


```html
<script type="text/javascript">
var x = document.getElementById("demo");
var lat;
var lng;

function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition);
    } else {
        x.innerHTML = "Geolocation is not supported by this browser.";
    }
}

function showPosition(position) {
  lat = position.coords.latitude;
  lng = position.coords.longitude;
    x.innerHTML = "Latitude: " + position.coords.latitude +
    "<br>Longitude: " + position.coords.longitude;
}
var pubnub = new PubNub({
  publishKey: 'demo',
  subscribeKey: 'demo'
});
setInterval(function(){
  pubnub.publish({
    channel: 'pubnub-mapbox',
    message: [{"latlng":[lat,lng]}]
  });

}, 10000);

</script>


```
