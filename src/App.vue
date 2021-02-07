<template>
  <div id="app">
    <div class="featured">
      <h1>A Te streamered</h1>
      <div class="option selected">
        <img :src="primaryStreamer.profile_image" />
        <h3>{{ primaryStreamer.name }}</h3>
      </div>
    </div>
    <div class="divider"></div>
    <div class="options">
      <h2>Válaszd ki, hogy kiket szeretnél még nézni (max. 3):</h2>
      <div
        class="option"
        v-for="streamer in secondaryStreamerOptions"
        :key="streamer.id"
        :class="{ selected: secondaryStreamers.indexOf(streamer.id) > -1 }"
        @click="toggleStreamerSelection(streamer.id)"
      >
        <img :src="streamer.profile_image" />
        <h3>{{ streamer.name }}</h3>
      </div>
    </div>
    <a :href="buttonUrl" target="_blank" class="button">Multistream indítása</a>
  </div>
</template>

<script>
import streamers from "./assets/streamers.json";

export default {
  name: "App",
  data: function () {
    return {
      primaryStreamer: null,
      secondaryStreamers: [],
      streamers: streamers,
    };
  },
  created() {
    this.streamers = this.fisherYates(this.streamers);
    this.primaryStreamer =
      this.streamers.find((s) => s.id === window.location.hash.substr(1)) ||
      streamers[0];
  },
  computed: {
    secondaryStreamerOptions() {
      return this.streamers.filter(
        (streamer) => streamer.id !== this.primaryStreamer.id
      );
    },
    buttonUrl() {
      const start =
        "https://multistre.am/" +
        this.primaryStreamer.id +
        "/" +
        this.secondaryStreamers.join("/");

      if (this.secondaryStreamers.length < 3) {
        return start + "/layout6/";
      }

      if (this.secondaryStreamers.length < 2) {
        return start + "/layout3/";
      }

      return start + "/layout10/";
    },
  },
  methods: {
    toggleStreamerSelection(streamerId) {
      const index = this.secondaryStreamers.indexOf(streamerId);
      if (index > -1) {
        this.secondaryStreamers.splice(index, 1);
        return;
      }

      if (this.secondaryStreamers.length > 2) {
        this.secondaryStreamers.splice(0, 1);
      }

      this.secondaryStreamers.push(streamerId);
    },
    changePrimaryStreamer(streamerId) {
      this.primaryStreamer = this.streamers.find((s) => s.id === streamerId);
      const index = this.secondaryStreamers.indexOf(streamerId);
      if (index > -1) {
        this.secondaryStreamers.splice(index, 1);
      }
    },
    fisherYates(array) {
      var count = array.length,
        randomnumber,
        temp;
      while (count) {
        randomnumber = (Math.random() * count--) | 0;
        temp = array[count];
        array[count] = array[randomnumber];
        array[randomnumber] = temp;
      }

      return array;
    },
  },
};
</script>
