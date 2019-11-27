<template>
  <div id="app">
    <p class="pretitle1">stay ahead of the curve with</p>
    <HelloWorld msg="The Amazing Hill Chart Drawing Tool"/>
    <HillChart :progress=42 />
    <!-- <img class="logo" src="http://logo.q42.com/q42-logo.svg" width="42" /> -->
  </div>
</template>

<script>
import HillChart from './components/HillChart'

document.title = "The Amazing Hill Chart Drawing Tool";

export default {
  name: 'app',
  components: {
    HillChart
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
/* .logo {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateY(-50%);
} */

.pretitle1 {
  font-size: 120%;
}

.pretitle2 {
  font-size: 130%;
}

</style>
