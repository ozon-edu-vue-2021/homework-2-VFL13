<template>
  <li @click.stop="updatePath" :class="{'selected': selected}">
    <div class="node">
      <span>
        <file-icons class="icon" :file-name="node.name"/>
      </span>
      {{ node.name }}
    </div>
  </li>
</template>

<script>
import mixinTreeElement from './mixins/mixinTreeElement'
import mixinFileLink from './mixins/mixinFileLink'

export default {
  name: "fileElement",
  mixins: [mixinTreeElement, mixinFileLink],
  components: {
    'file-icons': () => import('./icon/fileIcons'),
  }
}
</script>

<style scoped>

</style>