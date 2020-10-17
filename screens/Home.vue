<template>
   <scroll-view :content-container-style="{contentContainer: {
        paddingVertical: 20
    }}">
    <view class="statusbar" />

    <view class="control-container">
      <button title="Tomar Foto" @press="tomarFoto"></button>
      <image class="media-container" :source="{ uri: photoUri }" />
    </view>

    <view class="control-container">
      <button title="Tomar Video" @press="tomarVideo"></button>
      <video class="media-container" :source="{ uri: videoUri }" useNativeControls resizeMode="contain" />
    </view>
   </scroll-view>
</template>

<script>
import { Video } from "expo-av";
export default {
  data() {
    return {
      photoUri:
        "https://i.insider.com/5df126b679d7570ad2044f3e?width=1100&format=jpeg&auto=webp",
      videoUri:
        "http://techslides.com/demos/sample-videos/small.mp4"
    };
  },
  props: {
    navigation: {
      type: Object
    }
  },
  methods: {
    tomarFoto() {
      this.navigation.navigate("Camera", {
        onGoBack: this.refreshPhoto,
        tipo: "Foto"
      });
    },
    tomarVideo() {
      this.navigation.navigate("Camera", {
        onGoBack: this.refreshVideo,
        tipo: "Video"
      });
    },
    refreshPhoto(data) {
      this.photoUri = data;
    },
    refreshVideo(data) {
      this.videoUri = data;
    }
  },
  components: { Video }
};
</script>

<style>
.container {
  display: flex;
  align-items: center;
  justify-content: center;
}
.statusbar {
  background-color: white;
  height: 20;
}
.control-container {
  padding: 10;
}
.media-container {
  width: 300;
  height: 300;
}
</style>
