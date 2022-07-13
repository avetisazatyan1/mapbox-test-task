<template>
  <div id="app" class="app-container">
    <gmap-map
      :center="mapCenter"
      :zoom="10"
      :options="{
        zoomControl: false,
      }"
      style="width: 100%; height: 100%"
    >
      <gmap-polyline
        v-for="trail in trails"
        :key="trail.trailId"
        :path="trail.path"
        :options="{
          strokeColor: trail.trailColor,
        }"
        @click="selectTrail(trail.trailId)"
      ></gmap-polyline>
    </gmap-map>

    <div v-if="selectedTrail" class="selected-trail-info">
      <img
        v-if="selectedTrail.photos.smallThumb"
        :src="selectedTrail.photos.smallThumb"
        alt="trail image"
        class="trail-image"
      />
      <div class="trail-title">
        {{ selectedTrail.title }}
      </div>
    </div>
  </div>
</template>

<script>
import trailsJson from "./options/trails.json";
export default {
  name: "App",

  mixins: [trailsJson],

  data() {
    return {
      trails: [],
      mapCenter: {},
      selectedTrail: null,
    };
  },

  mounted() {
    trailsJson.forEach((trail) => {
      const newTrail = { ...trail };
      newTrail.path = [];
      const latArray = newTrail.track.latitude.split(",");
      const lonArray = newTrail.track.longitude.split(",");
      latArray.forEach((latitude, index) => {
        newTrail.path.push({
          lat: Number(latitude),
          lng: Number(lonArray[index]),
        });
      });
      newTrail.trailColor = this.generateRandomColor();

      this.trails.push(newTrail);
    });

    this.mapCenter = this.trails[0].path[0];
  },

  methods: {
    selectTrail(id) {
      this.selectedTrail = this.trails.find((trail) => trail.trailId === id);
    },

    generateRandomColor() {
      let maxVal = 0xffffff;
      let randomNumber = Math.random() * maxVal;
      randomNumber = Math.floor(randomNumber);
      randomNumber = randomNumber.toString(16);
      let randColor = randomNumber.padStart(6, 0);
      return `#${randColor.toUpperCase()}`;
    },
  },
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
}

.app-container {
  width: 100%;
  height: 100vh;
}

.selected-trail-info {
  background: black;
  width: 90%;
  height: 80px;
  position: absolute;
  display: flex;
  bottom: 30px;
  left: 5%;
  border-radius: 5px;
}

.trail-image {
  height: 80px;
  width: 80px;
  border-radius: 5px 0px 0px 5px;
}

.trail-title {
  color: white;
  padding-top: 10px;
  padding-left: 10px;
  letter-spacing: 1px;
}
</style>
