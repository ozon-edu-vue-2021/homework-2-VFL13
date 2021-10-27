<template>
  <div id="app">
    <Tree
      :node="nodeTree"
    />
    <ScrollToTop />
  </div>
</template>

<script>
import nodeTree from '../public/static/node_modules.json'

export default {
  name: 'App',
  components: {
    'ScrollToTop': () => import('./components/scroll-to-top'),
    'Tree': () => import('./components/tree'),
  },
  data() {
    return {
      nodeTree: null
    }
  },
  created() {
    this.nodeTree = nodeTree
  },
}
</script>

<style>
#app {
  margin: 0;
  padding: 0;
}
</style>
