<template>
  <div class="seller-input">
    <input
      v-if="!lazy"
      @blur="checkVal"
      type="text"
      ref="input"
      v-model.trim="val"
      :placeholder="placeholder"
      :style="width ? 'width:' + width + 'px' : ''"/>
    <input
      v-else
      @blur="checkVal"
      type="text"
      ref="input"
      v-model.trim.lazy="val"
      :placeholder="placeholder"
      :style="width ? 'width:' + width + 'px' : ''"/>
    <transition v-if="validate" name="fade-error">
      <span
        @click="clean(true)"
        v-show="isError"
        class="error-msg">{{errorMsg}}</span>
    </transition>
  </div>
</template>

<script>
export default {
  props: {
    placeholder: String,
    width: Number,
    value: {
      type: [String, Number],
      required: true
    },
    lazy: Boolean,
    rule: Array,
    changeCallBack: Function
  },
  data() {
    return {
      val: this.value,
      isError: false,
      errorMsg: '',
      timer: null
    };
  },
  computed: {
    validate() {
      return this.rule && this.rule.length > 0;
    }
  },
  watch: {
    val(v) {
      this.$emit('update:value', v);
      if (this.changeCallBack) this.changeCallBack();
    },
    value() {
      this.val = this.value;
    }
  },
  methods: {
    checkVal() {
      if (!this.validate) return;
      let aRule = this.rule;
      let i = 0;
      let l = aRule.length;
      let str = this.val;
      for (i; i < l; i++) {
        let res = aRule[i].pattern.exec(str) != null;
        if (res === false) {
          this.val = '';
          this.isError = true;
          this.errorMsg = aRule[i].msg;
          break;
        }
      }
      clearTimeout(this.timer);
      this.timer = setTimeout(() => {
        this.clean(false);
      }, 2200);
    },
    clean(isFocus) {
      if (this.isError) {
        this.val = '';
        this.isError = false;
        this.errorMsg = '';
        isFocus && this.$refs.input.focus();
      }
    }
  },
  beforeDestroy() {
    if (this.timer) {
      clearTimeout(this.timer);
      this.timer = null;
    }
  }
};
</script>

<style lang="scss">
.seller-input {
  position: relative;
  width: 216px;
  height: 36px;
  input {
    display: block;
    box-sizing: border-box;
    width: 100%;
    height: 100%;
    padding: 8px 8px;
    border: 1px solid $gray;
    border-radius: 2px;
    background-color: #ffffff;
    line-height: 1;
  }
  .error-msg {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    box-sizing: border-box;
    line-height: 34px;
    border: 1px solid red;
    border-radius: 2px;
    text-indent: 8px;
    color: red;
    background-color: #ffffff;
    cursor: text;
    overflow: hidden;
  }
  .fade-error-leave-active {
    transition: opacity .5s;
  }
  .fade-error-leave-to {
    opacity: 0;
  }
}
</style>
