<template>
  <form @submit.prevent="$emit('submit',$event)">
    <slot></slot>
  </form>
</template>

<script>

const Props = {
  rules: {
    type: Object,
    required: true
  },
  model: {
    type: Object,
    required: true
  }
}

export default {
  props: Props,
  provide() {
    return { form: this }
    // 抛出自己的索引  给儿子组件用
  },
  data() {
    return {
      fields: []
      // form-item的数组
    }
  },
  methods: {
    validate(name) {
      return new Promise(resolve => {
        let valid = true
        let count = 0
        // 没有名字的遍历一遍form-item 的验证
        // 有名字的找出特定的form-item 进行验证
        let fields = name ? this.fields.filter(field => field.prop === name) : this.fields
        fields.forEach(field => {
          field.validate(errors => {
            if (errors) (valid = false)
            if (++count === fields.length) resolve(valid)
          })
        })
      })
    },
    resetFields() {
      // 遍历form-item移除提示信息
      this.fields.forEach(field => {
        field.resetField()
      })
    }
  }
}
</script>

<style>
</style>
