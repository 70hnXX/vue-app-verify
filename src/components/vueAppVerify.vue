<template>
  <div class="vue-app-verify-container">
    <div class="vue-app-verify-container-labels">
      <div
          class="vue-app-verify-container-labels-items"
          v-for="(word,index) in codeArr"
          :key="index"
      >
        <input
            :style="dynamicStyle"
            :type="type"
            :id="'tb' + index"
            maxlength="1"
            v-model="codeArr[index]"
            @keyup="setValue(index)"
        />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'vue-app-verify',
  props: {
    length: {
      type: Number,
      default() {
        return 4
      }
    },
    enableWord: {
      type: Boolean,
      default() {
        return false
      }
    },
    type: {
      type: String,
      default() {
        return 'text'
      }
    }
  },
  data() {
    return {
      codeArr: [],
      isNumber: '^[0-9]{1}$',
      isNumberOrword: '^[A-Za-z0-9]{1}$'
    }
  },
  created() {
    // 根据props中的length来生成codeArr
    for (let i = 0; i < this.length; i++) {
      this.codeArr.push('')
    }
  },
  mounted() {
    // 默认光标定到第一个输入框
    document.getElementById('tb0').focus()
    // 绑定按键监听
    document.addEventListener('keydown', this.handleKeyDown)
  },
  beforeDestroy() {
    document.removeEventListener('keydown', this.handleKeyDown)
  },
  computed: {
    dynamicStyle() {
      let style = {
        border: 'none',
        outline: 'none',
        width: '30px',
        'line-height': '30px',
        'text-align': 'center',
        'font-size': '19px'
      }
      let styleStr = ''
      for (const key in style) {
        const element = style[key]
        styleStr += key + ':' + element + ';'
      }
      return styleStr
    }
  },
  methods: {
    handleKeyDown(event) {
      if (event.key === 'Backspace') {
        // 获取当前输入框的索引
        const currentIndex = this.codeArr.findIndex((value) => value === document.activeElement.value)
        if (currentIndex !== -1) {
          // 清空当前输入框并向前移动一格
          this.lastFocus(currentIndex)
        }
      }
    },
    setValue(index) {
      this.$forceUpdate()
      // 验证输入
      var reg = null
      if (this.enableWord) {
        reg = new RegExp(this.isNumberOrword)
      } else {
        reg = new RegExp(this.isNumber)
      }
      // 验证输入
      if (reg.test(this.codeArr[index])) {
        // 输入一个字母或者数字后,光标移动到下一个输入框
        this.nextFocus()
      } else {
        this.codeArr[index] = ''
      }
    },
    nextFocus() {
      let nextOne = this.codeArr.findIndex((value) => value == '')
      if (nextOne !== -1) {
        document.getElementById('tb' + nextOne).focus()
      } else {
        this.$emit('completed', this.codeArr.join(''))
      }
    },
    lastFocus(index) {
      if (index > 0) {
        // 清空当前输入框
        this.codeArr[index] = ''
        // 将光标移动到前一个输入框
        document.getElementById('tb' + (index - 1)).focus()
      }
    }
  }
}
</script>

<style lang="css">
.vue-app-verify-container-labels {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.vue-app-verify-container-labels-items {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 50px;
  height: 50px;
  text-align: center;
  border: solid 1px #333;
  border-radius: 6px;
  overflow: hidden;
}

.vue-app-verify-container-labels-items input {
  border: none;
  outline: none;
  width: 30px;
  line-height: 30px;
  text-align: center;
  font-size: 19px;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none !important;
  margin: 0;
}

</style>
