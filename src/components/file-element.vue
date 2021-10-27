<template>
  <li @click.stop="updatePath">
    <div class="node" :class="{'selected': selected}">
      <span>
        <Icon class="icon" :name="node.name"/>
      </span>
      {{ node.name }}
    </div>
  </li>
</template>

<script>
import MixinTreeElement from './mixins/mixin-tree-element'
import MixinFileLink from './mixins/mixin-file-link'

export default {
  name: "FileElement",
  mixins: [MixinTreeElement, MixinFileLink],
  components: {
    'Icon': () => import('./icon'),
  }
}
</script>

<style scoped>

</style>