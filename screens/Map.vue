<template>
  <view class="container">
    <text>Latitud: {{ coordinates.latitude }}</text>
    <text>Longitud: {{ coordinates.longitude }}</text>
    <touchable-opacity>
      <button title="Mi ubicación" @press="getLocation"></button>
    </touchable-opacity>
    <MapView class="mapa" :initial-region="coordinates" :onRegionChange="onRegionChange" ref="mapa">
      <Marker
        title="Tu localización"
        :coordinate="{
          latitude: coordinates.latitude,
          longitude: coordinates.longitude
          }"
        draggable
      />
    </MapView>
  </view>
</template>

<script>
import * as Permissions from "expo-permissions";
import * as Location from "expo-location";
import MapView, { Marker } from "react-native-maps";

import axios from "axios";

export default {
  data() {
    return {
      errorMessage: "",
      input: "",
      coordinates: {
        latitude: 41.881832,
        longitude: -87.623177,
        latitudeDelta: 0.1,
        longitudeDelta: 0.05
      },
      apiKey: "802371203359246534665x6018",
      locationText: ""
    };
  },
  mounted() {},
  methods: {
    getLocation() {
      Permissions.askAsync(Permissions.LOCATION)
        .then(status => {
          if (status !== "granted") {
            this.errorMessage = "Permission to access location was denied";
          }

          Location.getCurrentPositionAsync({}).then(location => {
            // this.coordinates.latitude = location.coords.latitude;
            // this.coordinates.longitude = location.coords.longitude;
            console.log(this.coordinates);
            this.$refs.mapa.animateToRegion(
              {
                latitude: location.coords.latitude,
                longitude: location.coords.longitude,
                latitudeDelta: 0.001,
                longitudeDelta: 0.05
              },
              10
            );
            // console.log(this.location.coords.latitude);
          });
        })
        .catch(err => {
          console.log(err);
        });
    },
    onRegionChange(region) {
      this.coordinates = region;
    },
    generateStreet() {
      axios
        .get(
          "https://geocode.xyz/" +
            this.coordinates.latitude +
            "," +
            this.coordinates.longitude +
            "?json=1&auth=" +
            this.apiKey
        )
        .then(response => {
          this.locationText = response.data.staddress;
          // console.log(response.data.staddress);
        })
        .catch(error => {
          console.log(error);
        });
    }
  },
  components: { MapView, Marker }
};
</script>

<style>
.container {
  flex: 1;
  background-color: white;
  align-items: center;
  justify-content: center;
}
.mapa {
  height: 300px;
  width: 300px;
}
.text-color-primary {
  color: blue;
}
.input{
    margin-top: 10;
    margin-bottom: 10;
    height: 40;
    width: 300;
    border-color: gray;
    border-width: 1;
}
</style>
