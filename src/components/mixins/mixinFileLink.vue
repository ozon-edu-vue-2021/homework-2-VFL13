<script>
export default {
  name: "mixinFileLink",
  props: {
    element: {
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
    selectedNode: {
      type: Object
    },
    last: {
      type: Boolean,
      default: true,
    },
  },
  methods: {
    // при клике по ноду, обновление путь к ней, передавая родителю
    // Отмечаем ноду как выделенную
    updatePath() {
      this.$emit('update-path',{add: true, path: this.element.name})
      this.setSelected({level: this.level, index: this.index})
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
          this.setSelected({level: this.level, index: this.index + 1})
        }
      }
    },
    // при переходе вверх с клавиатуры,
    // если элемент не первый, то обновляем выделенную ноду
    // если элемент первый, то переходим на уровень ниже
    moveUp() {
      if (this.selected) {
        if (this.index > 0) {
          this.setSelected({level: this.level, index: this.index - 1})
        }
        else if (this.index === 0) {
          this.$emit('close')
        }
      }
    }
  },
}
</script>
