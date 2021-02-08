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
      <h2>Válaszd ki, hogy kiket szeretnél még nézni:</h2>
      <div
        class="option"
        v-for="streamer in secondaryStreamerOptions"
        :key="streamer.id"
        :class="{ selected: secondaryStreamerIds.indexOf(streamer.id) > -1 }"
        @click="toggleStreamerSelection(streamer.id)"
      >
        <img :src="streamer.profile_image" />
        <h3>{{ streamer.name }}</h3>
      </div>
    </div>
    <a href="#" class="button" @click.prevent="startMultiStream">Multistream indítása</a>
  </div>
</template>

<script>
import streamers from "./assets/streamers.json";

export default {
  name: "App",
  data: function () {
    return {
      primaryStreamer: null,
      secondaryStreamerIds: [],
      streamers: streamers,
    };
  },
  created() {
    this.streamers = this.shuffle(this.streamers);
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
    selectedStreamerIds() {
      return [this.primaryStreamer.id, ...this.secondaryStreamerIds];
    },
    multiStreamUrl() {
      const start =
        "https://multistre.am/" +
        this.selectedStreamerIds.join("/");

      if (this.secondaryStreamerIds.length < 4) {
        return start + "/layout10/";
      }

      if (this.secondaryStreamerIds.length < 3) {
        return start + "/layout6/";
      }

      if (this.secondaryStreamerIds.length < 2) {
        return start + "/layout3/";
      }

      return start + "/layout14/";
    },
  },
  methods: {
    toggleStreamerSelection(streamerId) {
      const index = this.secondaryStreamerIds.indexOf(streamerId);
      if (index > -1) {
        this.secondaryStreamerIds.splice(index, 1);
        return;
      }

      if (this.secondaryStreamerIds.length > 3) {
        this.secondaryStreamerIds.splice(0, 1);
      }

      this.secondaryStreamerIds.push(streamerId);
    },
    shuffle(array) {
      let count = array.length,
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
    startMultiStream() {
      window.gtag('event', 'start_multistream', {
          'event_category': 'Click',
          'event_label': this.selectedStreamerIds.join("|"),
      });

      location.href = this.multiStreamUrl;
    },
  },
};
</script>
