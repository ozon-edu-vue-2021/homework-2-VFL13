<template>
  <li @click.stop="updatePath" :class="{'selected': selected}">
    <div class="node">
      <a :href="element.target" target="_blank">{{ element.name }}</a>
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
