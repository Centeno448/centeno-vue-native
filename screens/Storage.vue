<template>
  <view>
    <view class="container">
      <text-input class="text-input" v-model="text" />
      <touchable-opacity class="button" :on-press="add">
        <text class="button-text">Agregar</text>
      </touchable-opacity>
      <touchable-opacity class="button" :on-press="clearList">
        <text class="button-text">Limpiar</text>
      </touchable-opacity>
    </view>
    <view class="item-container" v-for="(item, index) in items" :key="index">
      <text class="text-container">{{ item }}</text>
      <touchable-opacity class="item-button" :on-press="() => remove(item)">
          <text class="button-text-delete">-</text>
      </touchable-opacity>
    </view>
  </view>
</template>

<script>
import { AsyncStorage } from "react-native";

export default {
  data() {
    return {
      text: "",
      items: []
    };
  },
  mounted() {
    this.get();
  },
  methods: {
    async add() {
      try {

        var error = this.verifyInput();

        if(error != ""){
          var errorMessage = this.handleError(error);
          alert(errorMessage);
          return;
        }

        await AsyncStorage.setItem(this.text, this.text);
        this.items.push(this.text);
        this.text = "";

      } catch (error) {
        console.log(error.message);
      }
    },
    async get() {
      try {
        const keys = await AsyncStorage.getAllKeys();
        this.items = keys;
      } catch (error) {
        console.log(error.message);
      }
    },
    async remove(item) {
      try {
        await AsyncStorage.removeItem(item);
        this.items = [];
        this.get();
      } catch (error) {
        console.log(error.message);
      }
    },
    async clearList() {
      try {
        await AsyncStorage.clear();
        this.items = [];
        alert("Listado limpiado.");
      } catch (error) {
        console.log(error.message);
      }
    },
    verifyInput() {
      if (this.items.includes(this.text)) {
        return "IS_DUPLICATE";
      }

      if(this.text.trim() == ""){
        return "IS_EMPTY";
      }

      return "";
    },
    handleError(error){
      switch(error){
        case "IS_DUPLICATE":
          return `'${this.text}' ya existe en el listado.`;

        case "IS_EMPTY":
          return "Por favor, ingrese un item.";

        default:
          break;
      }
    }

  }
};
</script>

<style>
.container {
  background-color: #333;
  flex-direction: row;
  padding: 20px;
}
.item-container {
  align-items: center;
  justify-content: center;
  padding-top: 10;
  flex-direction: row;
}
.text-input {
  background-color: white;
  margin-right: 5px;
  flex: 3;
}
.text-container {
  color: black;
  padding: 2;
  font-size: 20;
}
.button {
  flex: 1;
  background-color: #008080;
  margin-left: 5px;
  align-items: center;
  justify-content: center;
  padding-top: 10px;
  padding-bottom: 10px;
}
.button-text {
  color: white;
  font-weight: normal;
}
.item-button{
  width: 30;
  height: 30;
  background-color: #008080;
  margin-left: 5px;
  align-items: center;
  justify-content: center;
  border-radius: 50;
}
.button-text-delete{
  color: white;
  font-weight: bold;
  font-size: 30;
  margin-bottom: 5;
}
</style>
