<template>
  <li>
    <div class="node" :class="{'selected': selected}" @click.stop="clickOnFolder">
      <span>
        <Icon class="arrow" name="arrow" :class="isOpen ? 'rotate':''"></Icon>
        <Icon name="folder" :open="isOpen" class="icon"/>
      </span>
      {{ node.name }}
    </div>
    <!-- Use v-if or v-show -->
    <ul v-if="isOpen && node.contents.length" class="directory">
      <div v-for="(element, index) in node.contents" :key="`${index}-${element}`">
        <template v-if="element.type === 'directory'">
          <Folder
              :ref="element.name"
              :node="element"
              :last="isLast(index)"
              :level="level + 1"
              :index="index"
              :selectedNode="selectedNode"
              :nodeId="nodeId.concat([[level + 1, index]])"
              @close="closeFolder"
              @update-path="updatePath"
              @set-selected="setSelected"
          />
        </template>
        <template v-else-if="element.type === 'file'">
          <FileElement
              :ref="element.name"
              :node="element"
              :last="isLast(index)"
              :level="level + 1"
              :index="index"
              :selectedNode="selectedNode"
              :nodeId="nodeId.concat([[level + 1, index]])"
              @set-selected="setSelected"
              @close="closeFolder"
              @update-path="updatePath"
          />
        </template>
        <template v-else>
          <LinkElement
            :ref="element.name"
            :node="element"
            :last="isLast(index)"
            :level="level + 1"
            :index="index"
            :selectedNode="selectedNode"
            :nodeId="nodeId.concat([[level + 1, index]])"
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
import MixinTreeElement from './mixins/mixin-tree-element'

export default {
  name: "Folder",
  mixins: [MixinTreeElement],
  components: {
    'Icon': () => import('./icon'),
    'LinkElement': () => import('./link-element'),
    'FileElement': () => import('./file-element'),
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
      type: Array
    },
    nodeId: {
      type: Array,
      default: () => [[0,0]]
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
      console.log('On folder', this.isOpen )
      this.isOpen = !this.isOpen
      this.$emit('update-path',{add: this.isOpen, path: this.isOpen ? this.path : ''})
      if (this.node.contents.length) {
        this.setSelected(this.nodeId.concat([[this.level+1,0]]))
      }
      else {
        this.setSelected(this.nodeId)
      }
      if (!this.isOpen) {
        this.closeNode()
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
      this.setSelected(this.nodeId)
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
    toggleNode() {
      if (this.selected) {
        this.clickOnFolder()
      }
      else {
        const ref = this.node.contents[this.selectedNode[this.level + 1][1]].name
        this.$refs[ref][0].toggleNode()
      }
    },
    closeNode() {
      if (this.selected) {
        this.$emit('close')
      }
      else {
        const ref = this.node.contents[this.selectedNode[this.level + 1][1]].name
        this.$refs[ref][0].closeNode()
      }
    },
    moveDown() {
      if (this.selected) {
        if (!this.last) {
          let lastNode = this.nodeId[this.nodeId.length-1]
          lastNode = [lastNode[0], lastNode[1]+1]
          this.setSelected(this.nodeId.slice(0,this.nodeId.length-1).concat([lastNode]))
        }
      }
      else {
        const ref = this.node.contents[this.selectedNode[this.level + 1][1]].name
        this.$refs[ref][0].moveDown()
      }
    },
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
      else {
        const ref = this.node.contents[this.selectedNode[this.level + 1][1]].name
        this.$refs[ref][0].moveUp()
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