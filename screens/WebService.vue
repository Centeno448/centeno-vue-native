<template>
  <view class="container">
    <text>Nombre de usuario Github</text>
    <text-input
        class="input"
        v-model="usernameInput"
      />
    <button
        class="search-btn"
        :on-press="getGithubUser"
        title="Buscar"
    />

    <view class="user-data" v-if="gotUserSuccess">
      <text class="h2">Info de Usuario</text>
      <image class="avatar-container" :source="{ uri: avatar_url }"></image>
      <text>Nombre de usuario: {{username}}</text>
      <text>Nombre: {{name}}</text>
      <text class="last-user-label">Cantidad de repositorios públicos: {{num_repos}}</text>
      <button
      v-if="!gotReposSuccess"
        :on-press="getGithubRepos"
        title="Obtener Repositorios"
    />
    </view>

    <scroll-view v-if="gotReposSuccess" class="scroll-view" :content-container-style="{contentContainer: {
        paddingVertical: 20,
        alignItems: 'center',
        justifyContent: 'center'
    }}">
        <text class="h2 center-header">Info Repositorios</text>
        <view class="repo-container" v-for="repo in repos" :key="repo.id">
          <text>Nombre Repo: {{repo.name}}</text>
          <text>Descripción: {{repo.description}}</text>
          <text>Lenguaje de Programación: {{repo.language}}</text>
        </view>

    </scroll-view>

  </view>
</template>

<script>
import axios from "axios";
import { Alert } from 'react-native';
import { removeSubscription } from 'expo-media-library';

export default {
  data() {
    return {
      baseUrl: "https://api.github.com",
      gotUserSuccess: false,
      username: "",
      usernameInput: "",
      repos_url: "",
      name: "",
      num_repos: 0,
      avatar_url: "",
      gotReposSuccess: false,
      repos: []
    };
  },
  props: {
    navigation: {
      type: Object,
    },
  },
  methods: {
    getGithubUser(){
      var requestUrl = `${this.baseUrl}/users/${this.usernameInput}`;

      let config = {
        headers: {
          accept: "application/vnd.github.v3+json"
        }
      }

      axios.get(requestUrl, config)
      .then(res => {
        this.gotUserSuccess = true;

        this.setUserData(res);
      })
      .catch(error =>{
        this.gotUserSuccess = false;
        this.gotReposSuccess = false;
        console.log(error);
      });

    },
    setUserData(res){
        this.name = res.data.name;
        this.username = res.data.login;
        this.num_repos = res.data.public_repos;
        this.avatar_url = res.data.avatar_url;
        this.repos_url = res.data.repos_url;
        //console.log(res);
    },
    getGithubRepos(){
      var requestUrl = this.repos_url;

      let config = {
        headers: {
          accept: "application/vnd.github.v3+json"
        }
      }

      axios.get(requestUrl, config)
      .then(res => {
        this.gotReposSuccess = true;
        this.setRepoData(res);
        //console.log(res)
      })
      .catch(error =>{
        this.gotReposSuccess = false;
        console.log(error);
      });

    },
    setRepoData(res){
      this.repos = res.data;
    }
  }
};
</script>

<style>
.container {
  margin-top: 15;
  display: flex;
  align-items: center;
  justify-content: center;
}
.input{
    margin-top: 10;
    margin-bottom: 10;
    height: 40;
    width: 100;
    border-color: gray;
    border-width: 1;
}
.search-btn{
  margin-top: 10;
}
.h2{
  margin-bottom: 10;
  font-size: 18;
  font-weight: bold;
}
.user-data{
  margin-top: 20;
  align-items: center;
  justify-content: center;
}
.avatar-container {
  width: 50;
  height: 50;
}
.last-user-label {
  margin-bottom: 10;
}
.scroll-view{
  height: 150;
  width: 300;
  margin-top: 10;
}
.center-header{
  margin-left: 70;
}
.repo-container{
  margin-bottom: 10;
}
</style>
