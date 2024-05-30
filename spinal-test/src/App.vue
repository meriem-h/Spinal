<template>
  <main id="app">
    <Header />
    <Home />
  </main>
</template>

<script>
import Home from "./components/Home.vue"
import Header from "./components/Header.vue"

export default {
  name: 'App',
  components: {
    Home,
    Header
  },
}
</script>

<style>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  margin: 0;
}
</style>
