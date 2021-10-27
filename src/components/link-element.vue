<template>
  <li @click.stop="updatePath">
    <div class="node" :class="{'selected': selected}">
      <a :href="node.target" target="_blank">{{ node.name }}</a>
    </div>
  </li>
</template>

<script>
import MixinTreeElement from './mixins/mixin-tree-element'
import MixinFileLink from './mixins/mixin-file-link'

export default {
  name: "LinkElement",
  mixins: [MixinTreeElement, MixinFileLink]
}
</script>
