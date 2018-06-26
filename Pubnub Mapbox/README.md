# PubNub and MapBox

Subscribe and Publish to a Pubnub channel & visualize it on a Mapbox Map


##Publish to channel

Get the geolocation of the browser and publish the position coordinates to the pubnub channel every 10000 ms


Have a look at the [Pubnub Console](https://www.pubnub.com/docs/console) to see the datastrem in realtime.


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
