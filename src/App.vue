<template>
  <QRGenerator/>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import QRGenerator from './components/QRGenerator';

export default {
  name: 'App',
  components: {
    QRGenerator,
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 20px;
}
</style>
