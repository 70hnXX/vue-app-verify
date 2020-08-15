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
          type="text"
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
  name: "vue-app-verify",
  props: {
    length: {
      type: Number,
      default() {
        return 4;
      },
    },
    enableWord: {
      type: Boolean,
      default() {
        return false;
      },
    },
  },
  data() {
    return {
      codeArr: [],
      isNumber: "^[0-9]*$",
      isNumberOrword: "^[A-Za-z0-9]+$",
    };
  },
  created() {
    // 根据props中的length来生成codeArr
    for (let i = 0; i < this.length; i++) {
      this.codeArr.push("");
    }
  },
  mounted() {
    // 默认光标定到第一个输入框
    document.getElementById("tb0").focus();
  },
  computed: {
    dynamicStyle() {
      let style = {
        border: "none",
        outline: "none",
        width: "30px",
        "line-height": "30px",
        "text-align": "center",
        "font-size": "25px",
      };
      let styleStr = "";
      for (const key in style) {
        const element = style[key];
        styleStr += key + ":" + element + ";";
      }
      return styleStr;
    },
  },
  methods: {
    setValue(index) {
      this.$forceUpdate();
      // 验证输入
      var reg = null;
      if (this.enableWord) {
        reg = new RegExp(this.isNumberOrword);
      } else {
        reg = new RegExp(this.isNumber);
      }
      // 验证输入
      if (reg.test(this.codeArr[index])) {
        // 输入一个字母或者数字后,光标移动到下一个输入框
        this.nextFocus();
      } else {
        this.codeArr[index] = "";
      }
    },
    nextFocus() {
      let nextOne = this.codeArr.findIndex((value) => value == "");
      if (nextOne !== -1) {
        document.getElementById("tb" + nextOne).focus();
      } else {
        this.$emit("completed", this.codeArr.join(""));
      }
    },
  },
};
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
  font-size: 25px;
}
</style>