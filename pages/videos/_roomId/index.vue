<template>
  <div>
    <h1>{{ video.name }}</h1>
    <br />
    <p>Room ID: {{ video.roomId }}</p>
    <br />
    <p>Client ID: {{ video.clientId }}</p>
    <br />
    <p>Signaling Key: {{ video.signalingKey }}</p>
    <br /><button class="button--grey" v-on:click="connect">Connect</button>
    <button class="button--grey" v-on:click="disconnect">Disconnect</button>
  </div>
</template>

<script>
import {
  connection as AyameConnection,
  defaultOptions
} from "@open-ayame/ayame-web-sdk";

export default {
  props: ["video"],
  asyncdata() {
    return {
      conn: null
    };
  },
  computed: {
    conn() {
      return AyameConnection(this.props.wsUrl, this.props.roomId, options);
    }
  },
  head: {
    title: "Stream"
  }
};
</script>

<style></style>
