<template>
  <view class="container">
    <view class="statusbar"></view>
    <camera class="container" :type="cameraType" ratio="4:3" ref="camera" />
    <view class="camera-control-container">
      <touchable-opacity
        class="camera-control"
        :on-press="snapPhoto"
        v-if="tipoCamara == 'Foto'"
      >
        <fontAwesome class="iconos" name="camera-retro" />
      </touchable-opacity>

      <touchable-opacity
        class="camera-control"
        :on-press="stopVideo"
        v-if="isRecording"
      >
        <fontAwesome class="icono-stop" name="stop" />
      </touchable-opacity>

      <touchable-opacity
        class="camera-control"
        :on-press="recordVideo"
        v-else-if="tipoCamara == 'Video'"
      >
        <fontAwesome class="iconos" name="video-camera" />
      </touchable-opacity>

    </view>
  </view>
</template>

<script>
import * as Permissions from "expo-permissions";
import { Camera } from "expo-camera";
import * as ImagePicker from "expo-image-picker";
import * as MediaLibrary from "expo-media-library";
import { FontAwesome } from "@expo/vector-icons";
export default {
  data() {
    return {
      isRecording: false,
      hasPermission: false,
      tipoCamara: "",
      cameraType: "back",
    };
  },
  async mounted() {
    this.tipoCamara = this.navigation.state.params.tipo;

    await Permissions.askAsync(
      Permissions.CAMERA,
      Permissions.AUDIO_RECORDING,
      Permissions.CAMERA_ROLL
    )
      .then((status) => {
        this.hasPermission = status.status == "granted" ? true : false;
      })
      .catch((err) => {
        console.log(err);
      });
  },
  props: {
    navigation: {
      type: Object,
    },
  },
  methods: {
    async snapPhoto() {
      let photo = await this.$refs.camera.takePictureAsync();
      this.navigation.state.params.onGoBack(photo.uri, "Foto");
      this.navigation.goBack();
    },
    async recordVideo() {
      this.isRecording = true;
      let video = await this.$refs.camera.recordAsync();
      this.navigation.state.params.onGoBack(video.uri, "Video");
      this.navigation.goBack();
    },
    stopVideo() {
      this.$refs.camera.stopRecording();
      this.isRecording = false;
    },
  },
  components: { Camera, FontAwesome },
};
</script>

<style>
.statusbar {
  background-color: black;
  height: 50;
}
.container {
  flex: 1;
  background-color: black;
}
.camera-control-container {
  flex-direction: row;
  justify-content: center;
  margin: 20;
  margin-right: 30;
  margin-left: 30;
}
.camera-control {
  align-self: flex-end;
  align-items: center;
  background-color: transparent;
}
.iconos {
  color: white;
  font-size: 50;
}
.icono-stop{
  font-size: 50;
  color:red;
}
</style>
