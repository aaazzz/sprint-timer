<template>
  <div id="app">
    <Timer />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Timer from './components/Timer.vue';

@Component({
  components: {
    Timer,
  },
})
export default class App extends Vue {}
</script>

<style>
</style>
