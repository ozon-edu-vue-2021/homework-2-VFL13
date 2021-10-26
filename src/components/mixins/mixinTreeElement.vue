<script>
export default {
  name: "mixinTreeElement",
  methods: {
    // Передаем родителю level и index выбранной ноды
    setSelected(node) {
      this.$emit('set-selected', node)
    },
  },
  computed: {
    // Проверяем является ли нода выделенной
    selected() {
      return this.selectedNode.level === this.level
          && this.selectedNode.index === this.index
          && this.selectedNode.parent === this.parentName
          // && this.selectedNode.name === this.node.name
    },
  }
}
</script>