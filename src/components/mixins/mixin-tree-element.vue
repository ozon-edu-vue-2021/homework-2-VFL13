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
      return this.selectedNode ? this.nodeId.flat(2).join('') === this.selectedNode.flat(2).join('') : false
    },
  }
}
</script>