<template>
  <li @click.stop="updatePath">
    <div class="node" :class="{'selected': selected}">
      <a :href="node.target" target="_blank">{{ node.name }}</a>
    </div>
  </li>
</template>

<script>
import mixinTreeElement from './mixins/mixinTreeElement'
import mixinFileLink from './mixins/mixinFileLink'

export default {
  name: "linkElement",
  mixins: [mixinTreeElement, mixinFileLink]
}
</script>
