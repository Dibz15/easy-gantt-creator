<template>
  <v-btn
    :color="viewMode === handle ? 'accent' : undefined"
    elevation="2"
    small
    x-small
    v-on:click="onClick"
    >{{ title }}</v-btn
  >
</template>

<script>
import Vue from "vue";

export default Vue.extend({
  props: {
    title: {
      type: String,
      required: true,
    },
    handle: {
      type: String,
      required: true,
    },
    viewMode: {
      type: String,
      required: true,
    },
  },
  methods: {
    onClick() {
      this.$emit("click", this.handle);
    },
  },
});
</script>