<template>
  <div id="app">
    <h1>Calendar</h1>
    <Datepicker class="datepicker" />
  </div>
</template>

<script>
import Datepicker from "@/components/Datepicker.vue";

export default {
  name: "App",
  components: {
    Datepicker,
  },
};
</script>

<style lang="scss">
@import "@/assets/styles/main.scss";
</style>
