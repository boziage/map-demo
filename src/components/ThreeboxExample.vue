<template>
  <div id="map" />
</template>

<script>
import mapboxgl from "mapbox-gl";
import threebox from "threebox-plugin/dist/threebox";
import Pulse from "./Pulses/Pulse";

let pulse = new Pulse(400, 4000, true);

export default {
  name: "ThreeboxExample",
  data() {
    return {
      accessToken:
        "pk.eyJ1IjoiYW5kZXJzLWhvZWRob2x0IiwiYSI6ImNqNDlyMDFwZzBvMHgyeHBnazQwY2pieGsifQ.FCtWmm4xtFPeKQB33CeMeQ",
    };
  },
  mounted() {
    mapboxgl.accessToken = this.accessToken;

    this.map = new mapboxgl.Map({
      container: "map",
      interactive: true,
      style: "mapbox://styles/mapbox/satellite-v9",
      zoom: 14.5,
      center: [10.224666, 56.1654],
      pitch: 60,
      bearing: 270,
      antialias: true,
    }).on("style.load", () => {
      window.tb = new Threebox(
        this.map,
        this.map.getCanvas().getContext("webgl"),
        {
          defaultLights: true,
        }
      );

      let _this = this;

      if (this.map.getLayer("custom_layer") == null) {
        this.map.addLayer({
          id: "custom_layer",
          type: "custom",
          renderingMode: "3d",

          onAdd: function (map, mbxContext) {
            window.tb.add(_this.sphere(10.225531, 56.165746));
            window.tb.add(_this.sphere(10.219287, 56.1675));

            let pulseObj = window.tb
              .Object3D({ obj: pulse, units: "meters" })
              .setCoords([10.232191, 56.165543, 0]);

            pulseObj.setAnchor("bottom-left");

            window.tb.add(pulseObj);
          },
          render: function (gl, matrix) {
            window.tb.update();
            _this.animatePulse();
          },
        });
      }
    });
  },
  methods: {
    sphere(lon, lat) {
      let origin = [lon, lat, 0];
      return window.tb
        .sphere({ color: "red", material: "MeshToonMaterial" })
        .setCoords(origin);
    },
    animatePulse() {
      pulse.update();
      this.map.triggerRepaint();
    },
  },
};
</script>

<style scoped>
#map {
  width: 100%;
  height: 100%;
  padding: 0;
  margin: 0;
}
</style>