<template>
  <div id="app">
    <p class="pretitle1">stay ahead of the curve with</p>
    <div>
      <h1>
        {{ title }}<span class="version">{{ version }}</span>
      </h1>
    </div>
    <HillChart :progress="42" />

    <a class="q42-logo" href="http://q42.nl">
      <img src="http://logo.q42.com/q42-logo.svg" />
    </a>
    <footer>
      © 2020 <a href="http://q42.nl">Q42 bv</a> -
      <a href="https://github.com/Q42/hillchart-q42-nl">Github</a>
    </footer>
  </div>
</template>

<script>
import HillChart from './components/HillChart';
import { version } from '../package.json';

export default {
  name: 'app',
  components: {
    HillChart
  },
  data() {
    return {
      title: 'The Amazing Hill Chart Drawing Tool',
      version
    };
  },
  mounted() {
    document.title = this.title;
  }
};
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

.q42-logo {
  width: 42px;
  position: absolute;
  bottom: 42px;
  transform: translate3d(-50%, 0, 0);
  left: 50%;
}
footer {
  position: absolute;
  right: 0;
  left: 0;
  bottom: 10px;
  text-align: center;
  color: grey;
}
footer a {
  color: grey;
}

.version {
  vertical-align: super;
  font-size: 15px;
  margin-left: 10px;
  color: grey;
  font-weight: normal;
}
</style>
