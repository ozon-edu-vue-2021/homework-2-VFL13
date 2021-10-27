<script>
export default {
  name: "MixinFileLink",
  props: {
    node: {
      type: Object
    },
    level: {
      type: Number,
      default: 0
    },
    index: {
      type: Number,
      default: 0
    },
    last: {
      type: Boolean,
      default: true,
    },
    selectedNode: {
      type: Array
    },
    nodeId: {
      type: Array,
      default: () => [[0,0]]
    }
  },
  methods: {
    // при клике по ноду, обновление путь к ней, передавая родителю
    // Отмечаем ноду как выделенную
    updatePath() {
      this.$emit('update-path',{add: true, path: this.node.name})
      this.setSelected(this.nodeId)
    },
    // при выборе ноды с клавиатуры, обновление путь к ней, передавая родителю
    // Отмечаем ноду как выделенную
    toggleNode() {
      if (this.selected) {
        this.updatePath()
      }
    },
    // при переходе вниз с клавиатуры,
    // если элемент не последний, то обновляем выделенную ноду
    moveDown() {
      if (this.selected) {
        if (!this.last) {
          let lastNode = this.nodeId[this.nodeId.length-1]
          lastNode = [lastNode[0], lastNode[1]+1]
          this.setSelected(this.nodeId.slice(0,this.nodeId.length-1).concat([lastNode]))
        }
      }
    },
    // при переходе вверх с клавиатуры,
    // если элемент не первый, то обновляем выделенную ноду
    // если элемент первый, то переходим на уровень ниже
    moveUp() {
      if (this.selected) {
        if (this.index > 0) {
          let lastNode = this.nodeId[this.nodeId.length-1]
          lastNode = [lastNode[0], lastNode[1]-1]
          this.setSelected(this.nodeId.slice(0,this.nodeId.length-1).concat([lastNode]))
        }
        else if (this.index === 0) {
          this.$emit('close')
        }
      }
    },
    // при нажатии esc или кнопки влево, переход на уровень назад
    closeNode() {
      if (this.selected) {
        this.$emit('close')
      }
    }
  },
}
</script>
