<template>
  <div />
</template>

<script>
import Vue from 'vue'
import { getElementsByClassName } from '../util'
import { COMPONENT_NAME } from '../constant'
export default {
  data () {
    return {
      triggerDom: undefined,
      items: [],
      position: {
        x: 0,
        y: 0
      },
      style: {
        zIndex: 2,
        minWidth: 150
      },
      mainMenuInstance: null,
      customClass: null,
      mouseListening: false
    }
  },
  watch: {
    '$bus.isBlur': {
      handler: function (val) { if (val) this.$destroy() }
    }
  },
  mounted () {
    const SubmenuConstructor = Vue.component(COMPONENT_NAME)
    this.mainMenuInstance = new SubmenuConstructor()
    this.mainMenuInstance.items = this.items
    this.mainMenuInstance.commonClass = {
      menu: this.$style.menu,
      menuItem: this.$style.menu_item,
      clickableMenuItem: this.$style.menu_item__clickable,
      unclickableMenuItem: this.$style.menu_item__unclickable
    }
    this.mainMenuInstance.position = {
      x: this.position.x,
      y: this.position.y,
      width: 0,
      height: 0
    }
    this.mainMenuInstance.style.minWidth = this.style.minWidth
    this.mainMenuInstance.style.zIndex = this.style.zIndex
    this.mainMenuInstance.customClass = this.customClass
    this.mainMenuInstance.$mount()
    document.body.appendChild(this.mainMenuInstance.$el)
    this.addListener()
    this.triggerDomActive()
  },
  destroyed () {
    this.triggerDomDeactive()
    this.removeListener()
    if (this.mainMenuInstance) {
      this.mainMenuInstance.close()
    }
  },
  methods: {
    triggerDomDeactive () {
      if (this.triggerDom) this.triggerDom.className = this.triggerDom.className.replace(' contextmenu-active', '')
    },
    triggerDomActive () {
      if (this.triggerDom) this.triggerDom.className += ' contextmenu-active'
    },
    mousewheelListener () {
      this.$destroy()
    },
    mouseDownListener (e) {
      let el = e.target
      const menus = getElementsByClassName(this.$style.menu)
      while (!menus.find(m => m === el) && el.parentElement) {
        el = el.parentElement
      }
      if (!menus.find(m => m === el)) {
        this.$destroy()
      }
    },
    mouseClickListener (e) {
      let el = e.target
      const menus = getElementsByClassName(this.$style.menu)
      const menuItems = getElementsByClassName(this.$style.menu_item)
      const unclickableMenuItems = getElementsByClassName(
        this.$style.menu_item__unclickable
      )
      while (
        !menus.find(m => m === el) &&
        !menuItems.find(m => m === el) &&
        el.parentElement
      ) {
        el = el.parentElement
      }
      if (menuItems.find(m => m === el)) {
        if (e.button !== 0 || unclickableMenuItems.find(m => m === el)) {
          return
        }
        this.$destroy()
        return
      }
      if (!menus.find(m => m === el)) {
        this.$destroy()
      }
    },
    addListener () {
      if (!this.mouseListening) {
        document.addEventListener('click', this.mouseClickListener)
        document.addEventListener('mousedown', this.mouseDownListener)
        document.addEventListener('mousewheel', this.mousewheelListener)
        document.addEventListener('DOMMouseScroll', this.mousewheelListener)
        this.mouseListening = true
      }
    },
    removeListener () {
      if (this.mouseListening) {
        document.removeEventListener('click', this.mouseClickListener)
        document.removeEventListener('mousedown', this.mouseDownListener)
        document.removeEventListener('mousewheel', this.mousewheelListener)
        document.removeEventListener('DOMMouseScroll', this.mousewheelListener)
        this.mouseListening = false
      }
    }
  }
}
</script>

<style module>
.menu,
.menu_item,
.menu_item__clickable,
.menu_item__unclickable {
  box-sizing: border-box;
}
</style>
