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
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;800&display=swap');
#app {
  font-family: "Montserrat" sans-serif; 
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 20px;
}
body {
  background-color: rgb(0, 9, 24);
}
</style>
