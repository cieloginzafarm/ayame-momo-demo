<template>
  <div>
    <h1>{{ video.name }}</h1>
    <br />
    <p>Room ID: {{ video.roomId }}</p>
    <br />
    <p>Client ID: {{ options.clientId }}</p>
    <br />
    <p>Signaling Key: {{ signalingKey }}</p>
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
          v-model="mediaStream"
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
      conn: null,
      signalingUrl: "wss://ayame-lite.shiguredo.jp/signaling",
      signalingKey: "jPnJsAZWKSIro0toyuktmbuAJkzn6636TwibJKDXjN9OOjMU",
      options: defaultOptions,
      mediaStream: null,
      videoCodec: "none"
    };
  },
  methods: {
    async connect() {
      if (this.signalingKey) {
        this.options.signalingKey = this.signalingKey;
      }
      this.options.video.direction = "recvonly";
      this.options.audio.direction = "recvonly";
      this.options.video.codec = this.videoCodec;
      this.conn = AyameConnection(
        this.signalingUrl,
        this.video.roomId,
        this.options,
        true
      );
      console.log("CONNECTION JSON:");
      console.log(this.conn);
      try {
        await this.conn.connect({ key: this.signalingKey });
        this.conn.on("open", ({ authzMetadata }) => console.log(authzMetadata));
        this.conn.on("disconnect", e => console.log(e));
        this.conn.on("addstream", e => {
          console.log(e.stream);
          this.mediaStream.srcObject = e.stream;
        });
      } catch (error) {
        console.log("ERROR: ", error);
      }

      // // Set Stream Settings
      // if (this.videoCodec == "none") {
      //   this.videoCodec = null;
      // }
      // this.options.video.codec = this.videoCodec;
      // this.options.video.direction = "recvonly";
      // this.options.audio.direction = "recvonly";
      // if (this.video.signalingKey.length > 0) {
      //   this.options.signalingKey = this.video.signalingKey;
      // }
      // this.options.clientId = this.video.clientId ? this.video.clientId : this.options.clientId;
      // console.log(this.options)

      // // Start Stream Connection
      // console.log("Starting Video Connection....");
      // this.conn = AyameConnection(
      //   this.video.signalingUrl,
      //   this.video.roomId,
      //   this.options,
      //   true
      // );
      // console.log(this.conn);
      // await this.conn.connect({'key': this.conn.options.signalingKey});
      // this.conn.on("open", ({ authzMetadata }) => console.log(authzMetadata));
      // this.conn.on("disconnect", e => console.log(e));
      // this.conn.on("addstream", e => {
      //   this.mediaStream = e.stream;
      // });

      // console.log("Connected");
      // ./momo --no-audio-device --resolution 3840x2160 ayame wss://ayame-lite.shiguredo.jp/signaling cielojordanjp@farbot-test-1234 --signaling-key jPnJsAZWKSIro0toyuktmbuAJkzn6636TwibJKDXjN9OOjMU

      //?roomId=cielojordanjp@farbot-test-1234&signalingKey=jPnJsAZWKSIro0toyuktmbuAJkzn6636TwibJKDXjN9OOjMU
    },
    disconnect() {
      // this.conn.disconnect;
      if (this.conn) {
        this.conn.disconnect();
        this.conn = null;
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
