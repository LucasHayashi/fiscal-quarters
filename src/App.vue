<template>
  <FiscalQuarters />
  <MarketStream />
</template>

<script>
import FiscalQuarters from './components/FiscalQuarters.vue'
import MarketStream from './components/MarketStream.vue'

export default {
  name: 'App',
  components: {
    FiscalQuarters,
    MarketStream
  }
}
</script>

<style>
html {
  background-color: #bac1c7;
  max-width: 100%;
}

.container {
  max-width: 960px;
  margin: 0 auto;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

#app {
  font-family: "Poppins", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
