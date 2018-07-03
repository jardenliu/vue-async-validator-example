<template>
  <div>
    <!-- model 表单的数据模型  rules 验证规则  submit 表单提交事件 -->
    <vd-form ref="userForm" :model="userInfo" :rules="formRules" @submit="formValidate">
      <!-- prop 对应数据模型的名字  prop="name" 就是 userInfo.name -->
      <vd-form-item prop="name">
        <label>姓名：</label>
        <input name="username" v-model="userInfo.name" @blur="formValidateItem('name')" />
        <!-- @blur="formValidateItem('name')" 输入框离开表单时候  触发验证name这项 -->
      </vd-form-item>
      <vd-form-item prop="password">
        <label>密码：</label>
        <input name="password" type="password" v-model="userInfo.password" />
      </vd-form-item>

      <vd-form-item prop="custom">
        <label>自定义验证(可以异步)(年龄等于18岁)：</label>
        <input name="custom" type="number" v-model="userInfo.custom" />
      </vd-form-item>

      <vd-form-item>
        <button type="submit">提交</button>
        <button type="button" @click="resetValid">移除验证信息</button>
      </vd-form-item>
    </vd-form>
  </div>
</template>

<script>
import ValidateForm from './ValidateForm.vue'
import ValidateFormItem from './ValidateFormItem.vue'

export default {
  data() {
    const validateAge = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('年龄不能为空'))
      }
      // 模拟异步验证效果  也可以不异步
      setTimeout(() => {
        if (!Number.isInteger(parseInt(value))) {
          callback(new Error('请输入数字'))
        } else {
          if (parseInt(value) !== 18) {
            callback(new Error('必须等于18岁'))
          } else {
            callback()
          }
        }
      }, 2000)
    }

    return {
      formRules: {
        name: { type: 'string', required: true, message: `名字是必填的哟` },
        password: { type: 'string', required: true },
        custom: { validator: validateAge }
        // validator 自定义验证逻辑
        // type 支持一下这么多的数据类型验证   required  是否必填   message 提示信息

        // string: Must be of type string. This is the default type.
        // number: Must be of type number.
        // boolean: Must be of type boolean.
        // method: Must be of type function.
        // regexp: Must be an instance of RegExp or a string that does not generate an exception when creating a new RegExp.
        // integer: Must be of type number and an integer.
        // float: Must be of type number and a floating point number.
        // array: Must be an array as determined by Array.isArray.
        // object: Must be of type object and not Array.isArray.
        // enum: Value must exist in the enum.
        // date: Value must be valid as determined by Date
        // url: Must be of type url.
        // hex: Must be of type hex.
        // email: Must be of type email.
      },
      userInfo: {
        name: '',
        password: '',
        custom: 10
      }
    }
  },
  methods: {
    formValidate() {
      // 检测全部  不需要传名字
      this.$refs.userForm.validate().then(valid => {
        console.log(valid) // 成功返回true 失败返回false
      })
    },
    formValidateItem(name) {
      // 检测某一项，传某一项的 prop
      this.$refs.userForm.validate(name)
    },
    resetValid() {
      this.$refs.userForm.resetFields()
    }
  },
  components: {
    'vd-form': ValidateForm,
    'vd-form-item': ValidateFormItem
  }
}
</script>
