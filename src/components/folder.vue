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
      <div v-for="(element, index) in showNode" :key="`${index}-${element}`">
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
    },
    loadedNode: {
      type: Number,
      default: 30
    }
  },
  data() {
    return {
      isOpen: false,
      path: `${this.node.name}${this.node.type === 'directory' ? '/':''}`,
      loadedParts: 1

    }
  },
  computed: {
    nodesLength() {
      return this.node.contents.length
    },
    showNode() {
      if (this.nodesLength && this.nodesLength > 30) {
        return this.node.contents.slice(0,this.loadedNode * this.loadedParts)
      }
      else {
        return this.node.contents
      }

    },
    triggerNode() {
      return this.loadedNode > 50 ?  this.loadedNode - 30 : 20
    }
  },
  methods: {
    // Клик по папке,
    // если закрыта, открываем и выделяем первый элемент в ней
    // если открыта, закрываем и выделяем саму папку
    clickOnFolder() {
      this.isOpen = !this.isOpen
      this.$emit('update-path',{add: this.isOpen, path: this.isOpen ? this.path : ''})
      if (this.isOpen) {
        if (this.node.contents.length) {
          this.setSelected(this.nodeId.concat([[this.level+1,0]]))
        }
        else {
          this.setSelected(this.nodeId)
        }
      }
      else {
        this.setSelected(this.nodeId)
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
    },
    inViewPort() {
      if (this.isOpen) {
        const ref = this.node.contents[this.triggerNode - 1].name
        // console.log(ref)
        // console.log(this.$refs[ref][0])
        // console.log(this.$refs[ref][0].$el.getBoundingClientRect())
        const rect = this.$refs[ref][0].$el.getBoundingClientRect();

        if (
            rect.top >= 0 &&
            rect.left >= 0 &&
            rect.bottom <= (window.innerHeight || document.documentElement.clientHeight) && /* or $(window).height() */
            rect.right <= (window.innerWidth || document.documentElement.clientWidth) /* or $(window).width() */
        ) {
          this.loadedParts += 1
        }
      }
    }
  },
  mounted () {
    if (this.nodesLength && this.nodesLength > 30) {
      window.addEventListener('resize', this.inViewPort, false)
      window.addEventListener('scroll', this.inViewPort, false)
      window.addEventListener('touchmove', this.inViewPort, false)
    }
  },
  destroyed () {
    if (this.nodesLength && this.nodesLength > 30) {
      window.removeEventListener('resize', this.inViewPort)
      window.removeEventListener('scroll', this.inViewPort)
      window.removeEventListener('touchmove', this.inViewPort)
    }
  },
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