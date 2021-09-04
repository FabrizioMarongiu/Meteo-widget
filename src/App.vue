<template>
  <div id="app">
    <Widget />
  </div>
</template>

<script>
import Widget from './components/Widget.vue';


export default {
  name: 'App',
  components: {
    Widget,
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;500&display=swap');
*{
  margin:0;
  padding:0;
  box-sizing: border-box;
}
#app {
  height: 100vh;
  background-color: #f6f6f6;
  display:flex;
  justify-content: center;
  align-items: center;
  font-family: 'Montserrat', sans-serif;
}
</style>
