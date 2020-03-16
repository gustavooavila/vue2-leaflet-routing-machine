<template>
  <div id="app">
    <l-map :zoom="zoom" :center="center">
      <l-tile-layer :url="osmUrl" :attribution="attribution" />
      <l-routing-control :waypoints="waypoints"></l-routing-control>
    </l-map>
  </div>
</template>

<script>
import { LMap, LTileLayer } from "vue2-leaflet";

import LRoutingControl from "./components/LRoutingControl.vue";

const attribution =
  '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors';
const osmUrl = "http://{s}.tile.osm.org/{z}/{x}/{y}.png";
export default {
  components: { LMap, LTileLayer, LRoutingControl },
  data() {
    return {
      zoom: 6,
      center: { lat: 38.7436056, lng: -2.2304153 },
      osmUrl,
      attribution,
      waypoints: [
        { lat: 38.7436056, lng: -9.2304153 },
        { lat: 38.7436056, lng: -0.131281 }
      ]
    };
  }
};
</script>

<style>
@import "../node_modules/leaflet/dist/leaflet.css";
html,
body,
#app {
  height: 100%;
  margin: 0;
}
</style>