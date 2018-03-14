<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<style>
	body, html {
		font-family: 'Mukta Mahee', sans-serif;
		height: 100%;
	}
	#app {
		min-height: 100%;
    	height: 100%;
	}
</style>

<script>
  export default {
    name: 'vue-boilerplate',
  };
</script>
