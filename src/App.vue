<template>
  <div id="app">
    <custom-range v-model="left"></custom-range>
  </div>
</template>

<script>
import customRange from '@/components/custom-range';

export default {
  name: 'App',
  components: {
    customRange
  },
  data() {
    return {
      left: 34.5,
    }
  }
}
</script>

<style>
</style>
