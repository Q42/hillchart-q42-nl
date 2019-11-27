<template>
  <div id="app">
    <p class="pretitle1">stay ahead of the curve with</p>
    <div>
      <h1>{{title}}</h1>
    </div>
    <HillChart :progress=42 />
  </div>
</template>

<script>
import HillChart from './components/HillChart'

export default {
  name: 'app',
  components: {
    HillChart
  },
  data() { 
    return { title: "The Amazing Hill Chart Drawing Tool" }  
  },
  mounted() {
    document.title = this.title;
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

.pretitle1 {
  font-size: 120%;
}

.pretitle2 {
  font-size: 130%;
}

h3 {
  margin: 40px 0 0;
}
</style>
