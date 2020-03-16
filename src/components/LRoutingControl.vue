<template>
  <div style="display: none;">
    <slot v-if="ready"></slot>
  </div>
</template>

<script>
import L from "leaflet";
import { findRealParent } from "vue2-leaflet";
import "leaflet-routing-machine";

export default {
  data() {
    return {
      parentContainer: null,
      ready: false,
      routingLayer: null
    };
  },
  props: {
    waypoints: Array,
    plan: { type: null },
    router: { type: null },
    geocoder: { type: null },
    lineOptions: { type: Function },
    altLineOptions: { type: null },
    autoRoute: Boolean,
    routeLine: Function,
    waypointMode: String,
    routeDragInterval: Number,
    useZoomParameter: Boolean,
    showAlternatives: Boolean,
    routeWhileDragging: Boolean,
    fitSelectedRoutes: [String, Boolean]
  },
  beforeDestroy() {
    return this.routingLayer ? this.routingLayer.remove() : null;
  },
  methods: {
    add() {
      const {
        plan,
        router,
        geocoder,
        waypoints,
        routeLine,
        lineOptions,
        waypointMode,
        altLineOptions,
        useZoomParameter,
        showAlternatives,
        routeDragInterval,
        fitSelectedRoutes,
        routeWhileDragging
      } = this;

      // this right here is some nonsense
      // we are applying the rest operator on the result of the and operation
      // to make sure that properties are only added to the options object
      // if the value is defined
      // was getting some weird error messages saying that routeLine is undefined
      // this solves it
      const options = {
        ...(plan && { plan }),
        ...(router && { router }),
        ...(geocoder && { geocoder }),
        ...(waypoints && { waypoints }),
        ...(routeLine && { routeLine }),
        ...(lineOptions && { lineOptions }),
        ...(waypointMode && { waypointMode }),
        ...(altLineOptions && { altLineOptions }),
        ...(useZoomParameter && { useZoomParameter }),
        ...(showAlternatives && { showAlternatives }),
        ...(routeDragInterval && { routeDragInterval }),
        ...(fitSelectedRoutes && { fitSelectedRoutes }),
        ...(routeWhileDragging && { routeWhileDragging })
      };
      const { mapObject } = this.parentContainer;
      this.routingLayer = L.routing.control(options);

      // foward LMR events to the parent
      this.routingLayer.on("routingstart", e => {
        this.$emit("routingstart", e);
      });
      this.routingLayer.on("routesfound", e => {
        this.$emit("routesfound", e);
      });
      this.routingLayer.on("routingerror", e => {
        this.$emit("routingerror", e);
      });

      // this event is not documented as being part of the control class
      // but it is fired by the control class if being used with a plan
      this.routingLayer.on("waypointschanged", e => {
        this.$emit("waypointschanged", e);
      });

      this.routingLayer.addTo(mapObject);
    },
    // foward function calls to LRM
    getWaypoints() {
      return this.routingLayer.getWaypoints.apply(null, arguments);
    },
    setWaypoints() {
      return this.routingLayer.setWaypoints.apply(null, arguments);
    },
    spliceWaypoints() {
      return this.routingLayer.spliceWaypoints.apply(null, arguments);
    },
    getPlan() {
      return this.routingLayer.getPlan.apply(null, arguments);
    },
    getRouter() {
      return this.routingLayer.getRouter.apply(null, arguments);
    },
    route() {
      return this.routingLayer.route.apply(null, arguments);
    }
  },
  mounted() {
    this.parentContainer = findRealParent(this.$parent);
    this.add();
    this.ready = true;
  }
};
</script>
<style>
@import "../../node_modules/leaflet-routing-machine/dist/leaflet-routing-machine.css";
</style>