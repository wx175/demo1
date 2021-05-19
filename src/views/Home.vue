<template>
  <inputValidateForm @validateform-submit="submitFormHandle">
    <div class="col-md-4">
      <label class="form-label">手机号</label>
      <inputValidate :input-rules="phoneRules" type="number"></inputValidate>
    </div>

    <div class="col-md-4">
      <label class="form-label">邮箱</label>
      <inputValidate :input-rules="emailRules"></inputValidate>
    </div>

    <div class="col-md-4">
      <label class="form-label">密码</label>
      <inputValidate :input-rules="passwordRules" type="password" :model-value="'1234'"></inputValidate>
    </div>

    <!-- 自定义提交按钮 -->
    <!-- <template #form-sbumit-btn> <button>确认提交</button> </template> -->
  </inputValidateForm>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import inputValidateForm from '@/components/inputValidateForm.vue'
import inputValidate, { inputTypeRules } from '@/components/inputValidate.vue'

const phoneRules: inputTypeRules = [
  {
    type: 'phone',
    message: '手机号不合法'
  }
]

const emailRules: inputTypeRules = [
  {
    type: 'email',
    message: '邮箱不合法'
  }
]

const passwordRules: inputTypeRules = [
  {
    type: 'password',
    message: '输入的密码不合法'
  }
]

export default defineComponent({
  name: 'Home',
  components: {
    inputValidate,
    inputValidateForm
  },
  setup () {
    // 监听validateForm提交按钮
    const submitFormHandle = (result: boolean) => {
      if (result) {
        console.log('you can submit')
      }
    }

    return {
      phoneRules,
      emailRules,
      passwordRules,
      submitFormHandle
    }
  }
})
</script>
