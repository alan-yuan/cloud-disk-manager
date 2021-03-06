<template>
  <div
    class="file-dir-path-bar"
    :title="'当前目录：' + dir"
    @contextmenu.prevent="dirPathBarShowContextMenu"
    @dblclick="editing = true"
  >
    <div
      v-show="!editing"
      class="dir-path"
    >
      <span
        v-for="(dirName, idx) in dirList"
        :key="dirName + idx"
      >
        <span
          class="file-dirname"
          :style="dirList.slice(-1) == dirName ? 'font-weight: bold;' : ''"
          :title="`转到目录：${getTargetDir(idx)}`"
          @click="handleClickDirname(idx)"
        >{{ dirName }}</span>
        <span
          v-if="idx + 1 < dirList.length"
          class="file-sep"
        >{{ sep }}</span>
      </span>
    </div>
    <div
      v-show="editing"
      style="height: 100%;"
    >
      <input
        ref="dirInput"
        class="dir-input"
        :value="dir"
        @blur.prevent="editing = false"
        @keypress.enter="editing = false"
      >
    </div>
  </div>
</template>

<script>
const PATH = require('path')

export default {
  props: {
    dir: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      sep: PATH.sep,
      dirList: [],
      editing: false
    }
  },
  computed: {
    getTargetDir () {
      return idx => {
        // 取出目标路径
        const targetDir = this.dirList.slice(0, idx + 1).join(this.sep)
        // 若是根级目录，在路径最后方加上分隔符
        return targetDir.includes(this.sep) ? targetDir : targetDir + this.sep
      }
    }
  },
  watch: {
    dir (val) {
      this.dirList = this.dir.split(this.sep)
    },

    editing (val) {
      if (val === true) {
        this.$nextTick(() => {
          this.$refs.dirInput.focus()
        })
      } else {
        const targetDir = this.$refs.dirInput.value.trim()
        if (targetDir !== this.dir) this.$emit('changeDir', targetDir)
      }
    }
  },

  methods: {

    // 地址栏单级目录单击发射changeDir事件
    handleClickDirname (idx) {
      const targetDir = this.getTargetDir(idx)
      this.$emit('changeDir', targetDir)
    },

    // 地址栏右键单击上下文菜单
    dirPathBarShowContextMenu (event) {
      // 设置菜单
      this.$contextmenu({
        items: [
          {
            label: this.dir,
            disabled: true,
            divided: true
          },
          {
            label: '复制完整路径',
            onClick: () => {
              this.$bus.clipboard.writeText(this.dir)
            }
          },
          {
            label: '编辑路径（双击地址栏）',
            onClick: () => {
              this.editing = true
            }
          }
        ],
        event,
        zIndex: 3,
        minWidth: 230
      })
    },

    // 地址栏单项目右键单击上下文菜单
    dirItemShowContextMenu1 (event) {
      // 设置菜单
      this.$contextmenu({
        items: [
          {
            label: 'zzzz',
            onClick: () => {
              this.$bus.clipboard.writeText('this.currentDir')
            }
          }
        ],
        event,
        zIndex: 3,
        minWidth: 230
      })
    }
  }
}
</script>

<style scoped>
.file-dir-path-bar {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  margin-left: 8px;
  height: 36px;
  border-radius: 4px;
  color: #283593;
  background-color: #f1f2f6;
  display: flex;
  align-items: center;
  font-size: 13px;
}

.file-sep {
  margin-right: 1px;
}

.file-dirname {
  cursor: pointer;
}

.file-dirname:hover {
  filter: brightness(0.6);
  border-bottom: 1.2px dashed;
}

.dir-input {
  display: block;
  height: 100%;
  border: 2px solid #f1f1f1;
  padding: 0 15px;
  transition: 0.2s ease;
  border-radius: 4px;
  font-size: 14px;
  color: #283593;
}

.dir-input:hover {
  border-color: #7a94ff;
}

.dir-input:focus {
  border-color: #7a94ff;
}

.dir-path {
  padding: 0 15px;
}
</style>
