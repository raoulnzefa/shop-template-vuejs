<template>
  <div class="container" id="app">
      <Products></Products>
  </div>
</template>

<script>
import Products from './components/Products.vue';


export default {
  name: 'app',
  components: {
   Products
  }
}
</script>

<style lang="scss" scoped>

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
