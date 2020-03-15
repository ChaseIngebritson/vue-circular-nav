<template>
  <nav
    class="circular-nav"
    :style="`transform: rotate(${rotation}deg); width: ${size}px; height: ${size}px`"
    ref="nav">
    <div class="character-container" v-html="listHtml"/>
  </nav>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator'

@Component
export default class CircularNav extends Vue {
  @Prop({ default: 100 }) radius!: number
  @Prop({ default: 225 }) size!: number
  @Prop() rotation!: number;
  @Prop() items!: [string]

  private circle = null

  get listHtml () {
    let length = 0
    this.items.forEach((item: string) => {
      length += item.length
    })
    const deg = 360 / length

    let origin = 0
    return this.items.map((item: string, index: number) => {
      return item
        .split('')
        .map((char: string) => {
          const output = `<p class="nav-character" data-index="${index}" style="height:${this.radius}px;transform:rotate(${origin}deg);">${char}</p>`
          origin += deg
          return output
        })
        .join('')
    })
      .join('')
  }

  mounted () {
    const nav = this.$refs.nav as HTMLElement
    if (nav.firstChild !== null) {
      nav.firstChild.addEventListener('click', this.onNavClick)
    }
  }

  private onNavClick (event: Event) {
    const target = event.target as HTMLElement
    if (target && target.dataset.index) {
      const index = target.dataset.index
      this.$emit('nav-click', index)
    }
  }
}
</script>

<style lang="scss">
  nav.circular-nav {
    .character-container {
      transform: translate(50%, 50%);
    }

    .nav-character {
      display: block;
      position: absolute;
      transform-origin: 0 100%;
      cursor: pointer;
    }
  }
</style>
