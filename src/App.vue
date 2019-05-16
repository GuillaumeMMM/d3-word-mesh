<template>
  <div id="app">
    <ExampleMesh />
    <WordMesh />
  </div>
</template>

<script>
import WordMesh from './components/WordMesh.vue';
import ExampleMesh from './components/ExampleMesh.vue';

export default {
  name: 'app',
  components: {
    ExampleMesh,
    WordMesh
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
</style>
