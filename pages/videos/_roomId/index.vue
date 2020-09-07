<template>
  <div>
    <h1>{{ video.name }}</h1>
    <br />
    <p>ルームID: {{ video.roomId }}</p>
    <br />
    <p>クライアントID: {{ conn.options.clientId }}</p>
    <br />
    <p>シグナリングキー: {{ signalingKey }}</p>
    <br /><button class="button--grey" @click="connect">Connect</button>
    <button class="button--grey" @click="disconnect">Disconnect</button>
    <p>
      映像コーデック
      <select v-model="videoCodec" placeholder="videoCodec">
        <option value="none">none</option>
        <option value="H264">H264</option>
        <option value="VP8">VP8</option>
        <option value="VP9">VP9</option>
      </select>
      （Google Chrome、Microsoft Edge でのみで動作します）
    </p>
    <div>
      <div style="float:left;">
        <video
          id="remote-video"
          :srcObject.prop="this.mediaStream"
          autoplay
          playsinline
          controls
          style="width: 400px; height: 300px; border: 1px solid black;"
        ></video>
      </div>
      <br />
      <div>
        <pre>{{ this.conn }} </pre>
      </div>
    </div>
  </div>
</template>

<script>
import {
  connection as AyameConnection,
  defaultOptions
} from "@open-ayame/ayame-web-sdk";

export default {
  props: ["video"],
  data() {
    return {
      conn: AyameConnection("wss://ayame-lite.shiguredo.jp/signaling", this.video.roomId, this.defaultOptions),
      mediaStream: null,
      signalingKey: "jPnJsAZWKSIro0toyuktmbuAJkzn6636TwibJKDXjN9OOjMU",
      videoCodec: "none"
    };
  },
  methods: {
    async connect() {
      if (this.signalingKey) {
        this.conn.options.signalingKey = this.signalingKey;
      }
      this.conn.options.video.direction = "recvonly";
      this.conn.options.audio.direction = "recvonly";
      this.conn.options.video.codec = this.videoCodec;
      if (this.videoCodec == "none") {
        this.conn.options.video.codec = null;
      }
      try {
        this.conn.connect(this.mediaStream);
        this.conn.on("open", ({ authzMetadata }) => console.log(authzMetadata));
        this.conn.on("disconnect", e => console.log(e));
        this.conn.on("addstream", async e => {
          await console.log(e.stream);
          this.mediaStream = e.stream;
        });
      } catch (error) {
        console.log("ERROR: ", error);
      }
    },
    disconnect() {
      if (this.conn) {
        this.conn.disconnect();
        console.log("Disconnected");
      }
    }
  },
  head: {
    title: "Stream"
  }
};
</script>

<style></style>
