<template>
  <li>
    <div class="node" :class="{'selected': selected}" @click.stop="clickOnFolder">
      <span>
        <arrow class="arrow" :class="isOpen ? 'rotate':''"></arrow>
        <open-folder v-if="isOpen" class="icon"/>
        <close-folder v-else class="icon"/>
      </span>
      {{ node.name }}
    </div>
    <!-- Use v-if or v-show -->
    <ul v-if="isOpen && node.contents.length" class="directory">
      <div v-for="(element, index) in node.contents" :key="`${index}-${element}`">
        <template v-if="element.type === 'directory'">
          <folder
              :ref="element.name"
              :node="element"
              :last="isLast(index)"
              :level="level + 1"
              :index="index"
              :selectedNode="selectedNode"
              @close="closeFolder"
              @update-path="updatePath"
              @set-selected="setSelected"
          />
        </template>
        <template v-else-if="element.type === 'file'">
          <file-element
              :ref="element.name"
              :element="element"
              :last="isLast(index)"
              :level="level + 1"
              :index="index"
              :selectedNode="selectedNode"
              @set-selected="setSelected"
              @close="closeFolder"
              @update-path="updatePath"
          />
        </template>
        <template v-else>
          <link-element
            :ref="element.name"
            :element="element"
            :last="isLast(index)"
            :level="level + 1"
            :index="index"
            :selectedNode="selectedNode"
            @set-selected="setSelected"
            @close="closeFolder"
            @update-path="updatePath"
          />
        </template>
      </div>
    </ul>
  </li>
</template>

<script>
import mixinTreeElement from './mixins/mixinTreeElement'

export default {
  name: "folder",
  mixins: [mixinTreeElement],
  components: {
    'arrow': () => import('./icon/arrow'),
    'close-folder': () => import('./icon/closeFolder'),
    'open-folder': () => import('./icon/openFolder'),
    'link-element': () => import('./linkElement'),
    'file-element': () => import('./fileElement'),
  },
  props: {
    node: {
      type: Object
    },
    last: {
      type: Boolean,
      default: true,
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
    }
  },
  data() {
    return {
      isOpen: false,
      path: `${this.node.name}${this.node.type === 'directory' ? '/':''}`
    }
  },
  computed: {
    nodesLength() {
      return this.node.contents.length
    }
  },
  methods: {
    // Клик по папке,
    // если закрыта, открываем и выделяем первый элемент в ней
    // если открыта, закрываем и выделяем саму папку
    clickOnFolder() {
      this.isOpen = !this.isOpen
      this.$emit('update-path',{add: this.isOpen, path: this.isOpen ? this.path : ''})
      if (this.node.contents.length) {
        this.setSelected({level: this.level + 1, index: 0})
      }
      else {
        this.setSelected({level: this.level, index: this.index})
      }

    },
    // обновляем путь полученный из дочерней ноды
    updatePath(path) {
      this.$emit('update-path', {add: true, path: path.add ? this.path + path.path : this.path})
    },
    // вычисляем последнюю ноду, для обработки переходов вниз
    isLast(index) {
      return index === this.nodesLength - 1
    },
    // Закрыте папки,
    // закрываем и выделяем саму папку
    closeFolder() {
      this.isOpen = !this.isOpen
      this.$emit('update-path',{add: this.isOpen, path: this.isOpen ? this.path : ''})
      this.setSelected({level: this.level, index: this.index})
    },
    // toggleNode(path), closeNode(path), moveDown(path), moveUp(path)
    // обрабатываем переход с клавиатуры,
    // если это выбранная папка, выполняем дейсвие с ней
    // если нет, передаем событие дальше(
    //    если выбранная нода на уровне дочерних элементов, выбираем элемент по индексу,
    //    и передаем событие в него,
    //    если уровень выше дочерних, передаем по имени элемента из пути path
    //    в соответствии с уровнем
    // )
    toggleNode(path) {
      if (this.selected) {
        this.clickOnFolder()
      }
      else {
        if (this.selectedNode.level === this.level + 1) {
          const ref = this.node.contents[this.selectedNode.index].name
          this.$refs[ref][0].toggleNode(path)
        }
        else if (this.selectedNode.level > this.level + 1) {
          this.$refs[path[this.level + 1]][0].toggleNode(path)
        }
      }
    },
    closeNode(path) {
      if (this.selected) {
        this.$emit('close')
      }
      else {
        if (this.selectedNode.level === this.level + 1) {
          const ref = this.node.contents[this.selectedNode.index].name
          this.$refs[ref][0].closeNode(path)
        }
        else if (this.selectedNode.level > this.level + 1) {
          this.$refs[path[this.level + 1]][0].closeNode(path)
        }
      }
    },
    moveDown(path) {
      if (this.selected) {
        if (!this.last) {
          this.setSelected({level: this.level, index: this.index + 1})
        }
      }
      else {
        if (this.selectedNode.level === this.level + 1) {
          const ref = this.node.contents[this.selectedNode.index].name
          this.$refs[ref][0].moveDown(path)
        }
        else if (this.selectedNode.level > this.level + 1) {
          this.$refs[path[this.level + 1]][0].moveDown(path)
        }
      }
    },
    moveUp(path) {
      if (this.selected) {
        if (this.index > 0) {
          this.setSelected({level: this.level, index: this.index - 1})
        }
        else if (this.index === 0) {
          this.$emit('close')
        }
      }
      else {
        if (this.selectedNode.level === this.level + 1) {
          const ref = this.node.contents[this.selectedNode.index].name
          this.$refs[ref][0].moveUp(path)
        }
        else if (this.selectedNode.level > this.level + 1) {
          this.$refs[path[this.level + 1]][0].moveUp(path)
        }
      }
    }
  }
}
</script>

<style scoped>
.arrow {
  margin-right: 1rem;
  transition: all 0.2s ease;
}
.rotate {
  transform: rotate(90deg)
}
</style>