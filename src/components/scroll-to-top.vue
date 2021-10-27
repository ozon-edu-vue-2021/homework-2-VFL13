<template>
  <a @click="scrollTop" v-show="visible" class="bottom-right">
    <Icon name="arrow" class="icon up"></Icon>
  </a>
</template>

<script>
import Icon from "./icon";
export default {
  name: "ScrollToTop",
  components: {Icon},
  data () {
    return {
      visible: false
    }
  },
  methods: {
    scrollTop() {
      this.intervalId = setInterval(() => {
        if (window.pageYOffset === 0) {
          clearInterval(this.intervalId)
        }
        window.scroll(0, window.pageYOffset - 50)
      }, 20)
    },
    scrollListener() {
      this.visible = window.scrollY > 150
    }
  },
  mounted: function () {
    window.addEventListener('scroll', this.scrollListener)
  },
  beforeDestroy: function () {
    window.removeEventListener('scroll', this.scrollListener)
  }
}
</script>

<style scoped>
.bottom-right {
  position: fixed;
  bottom: 2rem;
  right: 2rem;
  cursor: pointer;
}
.up {
  transform: rotate(270deg)
}
</style>