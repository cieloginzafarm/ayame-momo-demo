<template>
  <div class="container">
    <h1 type="title">Ayame-Momo ビデオデモ</h1>
    <div class="list" v-for="video in videos" :key="video.roomId">
      <!-- <br>Room ID: <input type="text" v-model="video.roomId" />
      <br>Client ID: <input type="text" v-model="video.clientId" />
      <br>Signaling Key: <input type="text" v-model="video.signalingKey" />
      <br><button class="button--grey" v-on:click="connect">Connect</button>
      <button class="button--grey" v-on:click="disconnect">Disconnect</button> -->
      <nuxt-link :to="`/videos/${video.roomId}`">{{ video.name }}</nuxt-link>
    </div>
    <nuxt-child />
    
  </div>
</template>

<script>
import {
  connection as AyameConnection,
  defaultOptions
} from "@open-ayame/ayame-web-sdk"
import videos from "@/assets/videos.json"

export default {
  data() {
    return {
      videos
    };
  },
  head() {
    return {
      title: "Ayame-Momo ビデオデモ"
    };
  }
};
</script>

<style>
.container {
    display: flex block;
    padding: 50px;
}

.list {
  padding-left: 20px;
}
</style>
