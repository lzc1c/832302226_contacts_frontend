<template>
  <div id="app">
    <ContactsView />
  </div>
</template>

<script>
import ContactsView from './views/ContactsView.vue'

export default {
  name: 'App',
  components: {
    ContactsView
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0;
  padding: 0;
}
</style>
