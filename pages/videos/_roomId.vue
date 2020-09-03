<template>
  <div>
      <nuxt-child :video="video" />
  </div>
</template>

<script>
import videos from "@/assets/videos.json"

export default {
    head() {
        return {
            titleTemplate: `%s ${this.video.name}`
        }
    },
    data() {
        return {
            videos
        }
    },
    computed: {
        video() {
            return this.videos.find(v => v.roomId == this.$route.params.roomId)
        }
    }

}
</script>

<style>

</style>