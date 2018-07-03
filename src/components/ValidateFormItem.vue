<template>
  <div>
    <slot></slot>
    <div v-show="validateState == 'error'" class="validate-message-eror">
      {{validateMessage}}
    </div>
  </div>
</template>

<script>
import AsyncValidator from 'async-validator'

const Props = {
  prop: String,
  rule: [Object, Array]
}
export default {
  data() {
    return {
      validateState: '',
      validateMessage: ''
    }
  },
  props: Props,
  inject: ['form'],
  mounted() {
    if (this.prop) {
      // 挂载的时候，把自己加到ValidateForm的fields里面去
      this.form.fields.push(this)
    }
  },
  beforeDestroy() {
    // 移除的时候，把自己从ValidateForm的fields里面去掉
    if (this.prop) this.form.fields.splice(this.form.fields.indexOf(this, 1))
  },
  methods: {
    getRule() {
      return { [this.prop]: this.rule || this.form.rules[this.prop] || {} }
    },
    validate(callback) {
      // 进行验证
      const validator = new AsyncValidator(this.getRule())
      validator.validate({ [this.prop]: this.form.model[this.prop] }, errors => {
        this.validateState = !errors ? 'success' : 'error'
        this.validateMessage = errors ? errors[0].message : ''
        callback(this.validateMessage)
      })
    },
    resetField() {
      // 重置验证状态
      this.validateState = ''
      this.validateMessage = ''
    }
  }
}
</script>

<style>
.validate-message-eror {
  color: red;
  font-size: 12px;
}
</style>
