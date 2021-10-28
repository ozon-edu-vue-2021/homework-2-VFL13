<template>
  <div>
    <div class="path">
      <span><Icon name="arrow" class="icon"/></span>
      <h3>
        /{{ path }}
      </h3>
    </div>
    <div class="tree">
      <ul>
        <Folder
            ref="root"
            :node="node"
            :last="true"
            :selectedPath="path"
            :selectedNode="selectedNode"
            @update-path="updatePath"
            @set-selected="setSelected"
        />
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "Tree",
  props: {
    node: {
      type: Object,
    },
  },
  components: {
    'Icon': () => import('./icon'),
    'Folder': () => import('./folder'),
  },
  data() {
    return {
      path: '',
      // selectedNode - выделенная ноды, массивом из координат
      // - последовательность уровня и индекс элемента, первый [0,0]
      selectedNode: null,
    }
  },
  computed: {
    pathByNode() {
      // путь к выбранной ноде в виде массива
      return this.path.split('/')
    }
  },
  methods: {
    updatePath(path) {
      // обновляем путь к выбранной ноде
      this.path = path.path
    },
    setSelected(node) {
      // обновляем level и index выделенной ноды
      this.selectedNode = node
    },
    handleKeyUp(event) {
      event.preventDefault()

      if (event.keyCode === 13 || event.keyCode === 39) {
        // Ждем нажатия "Enter" or "->"
        // Если не выбрана ни одна нода, выбиваем корень
        if (this.selectedNode === null) {
          this.selectRootNode()
        }
        else {
          // Если выбрана нода, тригерим у корня событие toggleNode
          // с путем к выбранной ноде
          this.$refs.root.toggleNode(this.pathByNode)
        }
      }
      else if (event.keyCode === 27 || event.keyCode === 37) {
        // Ждем нажатия "Escape" or "<-"
        // Если не выбрана ни одна нода, выбиваем корень
        if (this.selectedNode === null) {
          this.selectRootNode()
        }
        // Если выбрана корневая нода, сбрасываем выделение
        else if (this.selectedNode.flat(2).join('') === '00') {
          this.selectedNode = null
        }
        // Если выбрана не корневая нода, тригерим у корня событие closeNode
        // с путем к выбранной ноде
        else {
          this.$refs.root.closeNode(this.pathByNode)
        }
      }
      else if (event.keyCode === 38) {
        // Ждем нажатия "Arrow Up"
        // Если не выбрана ни одна нода, выбиваем корень
        if (this.selectedNode === null) {
          this.selectRootNode()
        }
        // Если выбрана нода, тригерим у корня событие moveDown
        // с путем к выбранной ноде
        else {
          this.$refs.root.moveUp(this.pathByNode)
        }
      }
      else if (event.keyCode === 40) {
        // Ждем нажатия "Arrow Down"
        // Если не выбрана ни одна нода, выбиваем корень
        if (this.selectedNode === null) {
          this.selectRootNode()
        }
        // Если выбрана нода, тригерим у корня событие moveDown
        // с путем к выбранной ноде
        else {
          this.$refs.root.moveDown(this.pathByNode)
        }
      }
    },
    selectRootNode() {
      this.selectedNode = [0,0]
    },
  },
  mounted () {
    window.addEventListener('keyup', this.handleKeyUp, false)
  },
  destroyed () {
    window.removeEventListener('keyup', this.handleKeyUp)
  },
}
</script>

<style>
  h3{
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
    width: calc(99% - 2rem);
  }
  h3:hover{
    text-overflow: unset;
    white-space: normal;
  }
  span {
    padding-left: 1rem;
    padding-right: 1rem;
    display: inline-block;
  }
  .path {
    display: flex;
    align-items: center;
    overflow: hidden;
    position: fixed;
    border: solid;
    margin: 5px;
    padding: 5px;
    top: 0;
    left: 0;
    width: calc(100% - 40px);
    background: white;
    font-family: 'Roboto Mono', monospace;
    font-size: 1.5rem;
    font-weight: 400;
    line-height: 2rem;
    color: #212529;
    z-index: 2;
  }
  .tree {
    position: relative;
    background: white;
    margin-top: 120px;
    padding: 0px;
    font-family: 'Roboto Mono', monospace;
    font-size: 2rem;
    font-weight: 400;
    line-height: 1.5;
    color: #212529;
  }
  .tree span {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    margin-right: 1rem;
  }
  .tree ul {
    padding-left: 1rem;
    list-style: none;
  }
  .tree ul li {
    position: relative;
    padding-top: 5px;
    padding-bottom: 5px;
    padding-left: 15px;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
  }

  .icon {
    height: 2rem;
    width: 2rem;
    display: inline;
  }
  .node {
    display:inline-flex;
    align-items: center;
    justify-content: center;
    padding-left: 15px;
    padding-right: 15px;
  }
  .node:hover {
    background-color: #b4c4c4;
  }
  .selected {
    background-color: #95A5A5;
  }
</style>
