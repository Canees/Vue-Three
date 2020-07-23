<template>
  <div id="app">
    <car/>
  </div>
</template>

<script>
import car from './components/carhead.vue'

export default {
  name: 'App',
  components: {
    car
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  color: #2c3e50;
}
</style>
