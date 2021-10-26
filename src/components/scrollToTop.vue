<template>
  <a @click="scrollTop" v-show="visible" class="bottom-right">
    <arrow class="icon up"></arrow>
  </a>
</template>

<script>
import Arrow from "./icon/arrow";
export default {
  name: "scroll-to-top",
  components: {Arrow},
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