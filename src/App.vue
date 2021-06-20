<template>
  <div id="app">
    <img alt="Vue logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/International_Pok%C3%A9mon_logo.svg/1024px-International_Pok%C3%A9mon_logo.svg.png" height="200">
    <PokemonTable title="Welcome to your pokemon index"/>
  </div>
</template>

<script>
import PokemonTable from './components/PokemonTable.vue'

export default {
  name: 'App',
  components: {
    PokemonTable
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
