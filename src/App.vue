<template>
  <div id="app">
    <transition name="router-fade" mode="out-in">
        <router-view ></router-view>
    </transition>
  <!-- <palfooter></palfooter> -->
  </div>
</template>

<script>
// import palfooter from './components/footer.vue'
// import palheader from './components/header.vue'
export default {
  name: 'App',
  components: {
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
