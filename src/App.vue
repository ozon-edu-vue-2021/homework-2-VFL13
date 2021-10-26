<template>
  <div id="app">
    <tree
      :nodeTree="$options.nodeTree"
    />
    <scroll-to-top />
  </div>
</template>

<script>
import nodeTree from '../public/static/node_modules.json'
import ScrollToTop from "./components/scrollToTop";

export default {
  name: 'App',
  components: {
    ScrollToTop,
    'tree': () => import('./components/tree'),
  },
  created() {
    this.$options.nodeTree = nodeTree
  },
}
</script>

<style>
#app {
  margin: 0;
  padding: 0;
}
</style>
