<template>
  <div id="#app">
    <header-view></header-view>
    <router-view></router-view>
  </div>
</template>

<script>
  import store from 'renderer/vuex/store';
  import HeaderView from 'components/Header.vue';
  export default {
    store,
    components: {
      HeaderView,
    },
  };
</script>

<style>

  @import url(https://fonts.googleapis.com/css?family=Lato:300);

  * {
    margin: 0;
    padding: 0;
  }

  html,
  body,
  #app { height: 100%; }

  body {
    align-items: center;
    background:
      radial-gradient(
        ellipse at center,
        rgba(255, 255, 255, 1) 0%,
        rgba(229, 229, 229, .85) 100%
      );
    background-position: center;
    font-family: Lato, Helvetica, sans-serif;
    justify-content: center;
    text-align: center;
  }
</style>
