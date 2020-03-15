<template>
  <div class="home">
    <CircularNav
      :rotation="navRotation"
      :items="navItems"
      @nav-click="onNavClick($event)" />

    <div class="heading">

    </div>

    <div
      class="section"
      v-for="(item, index) in navItems"
      :key="index"
      :ref="`section-${index}`">
      <h1>Section {{ index + 1 }}</h1>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import CircularNav from '@/components/CircularNav.vue'

@Component({
  components: {
    CircularNav
  }
})
export default class Home extends Vue {
  private navRotation = -25
  private navItems = [
    'Section 1 | ',
    'Section 2 | ',
    'Section 3 | ',
    'Section 4 | ',
    'Section 5 | '
  ]

  private allowRotation = true

  private created () {
    window.addEventListener('scroll', this.onScroll)
  }

  private destroyed () {
    window.removeEventListener('scroll', this.onScroll)
  }

  private onScroll () {
    if (!this.allowRotation) {
      return false
    }

    this.navItems.forEach((item, index) => {
      const target = (this.$refs[`section-${index}`] as [HTMLElement])[0]

      if (window.scrollY >= target.offsetTop) {
        const deg = -((360 / this.navItems.length) * index) + -25
        this.navRotation = deg
      }
    })
  }

  private onNavClick (index: number) {
    if (!this.allowRotation) {
      return false
    }

    const target = (this.$refs[`section-${index}`] as [HTMLElement])[0]
    const offset = target.offsetTop

    const deg = -((360 / this.navItems.length) * index) + -25
    this.navRotation = deg

    this.allowRotation = false
    this.scrollTo(offset)
      .then(() => {
        this.allowRotation = true
      })
  }

  // https://stackoverflow.com/questions/52292603/is-there-a-callback-for-window-scrollto
  private scrollTo (offset: number) {
    return new Promise((resolve) => {
      const onScroll = function () {
        if (window.pageYOffset === offset) {
          window.removeEventListener('scroll', onScroll)
          resolve()
        }
      }
      window.addEventListener('scroll', onScroll)
      onScroll()
      window.scrollTo({
        top: offset,
        behavior: 'smooth'
      })
    })
  }
}
</script>

<style scoped lang="scss">
nav.circular-nav {
  position: fixed;
  left: 75px;
  top: 25px;
  transition: 0.3s ease;

  li {
    cursor: pointer;
  }
}

.heading {
  height: 50vh;
}

.section {
  width: 100%;
  height: 100vh;

  &:nth-child(odd) {
    background-color: gray;
  }
}
</style>
