<template>
  <div
    id="topbar-box"
    ref="topbar"
    :style="`height: ${$bus.topbarHeight}px`"
  >
    <div id="topbar-title">
      <img
        src="@/assets/logo.png"
        style="height: 40px; margin-right: 10px;"
        @mousedown="e=>{e.prevent.default()}"
      >
      {{ $bus.appName }}
    </div>
    <div id="topbar-action-box">
      <control-button
        icon-class="button-hidden"
        @click="win.minimize()"
      />
      <control-button
        icon-class="button-fullsize"
        @click="!win.isMaximized() ? win.maximize() : win.restore()"
      />
      <control-button
        icon-class="button-exit"
        type="warning"
        @click="handleWinClose"
      />
    </div>
  </div>
</template>

<script>
import controlButton from 'components/controlButton.vue'

export default {
  components: {
    controlButton
  },
  props: {
    blur: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      win: this.$electron.remote.getCurrentWindow()
    }
  },
  watch: {
    blur (val) {
      if (val) this.$refs.topbar.className += 'on-blur'
      else this.$refs.topbar.className = ''
    }
  },
  methods: {
    handleWinClose () {
      this.$electron.ipcRenderer.sendTo(
        this.$bus.getSubService('chokidarService').win.id,
        'closeAllWatcher',
        { componentId: 'topbar' }
      )
      this.win.close()
    }
  }
}
</script>

<style lang="less" scoped>
// topbar.vue
#topbar-box {
  display: flex;
  -webkit-app-region: drag;
  user-select: none;
}

.on-blur {
  //filter: brightness(.9);
}

#topbar-title {
  display: flex;
  align-items: center;
  flex: 1;
  padding: 0 20px;
  line-height: 18px;
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 0.35px;
}

#topbar-action-box {
  padding: 0 10px;
  display: flex;
  align-items: center;
  -webkit-app-region: no-drag;
}

.topbar-action-button {
  align-items: center;
  justify-content: center;
  display: flex;
  transition: 0.2s ease;
  height: 34px;
  width: 34px;
  font-size: 16px;
  border-radius: 4px;
  margin-right: 2px;
  cursor: pointer;
}
</style>
