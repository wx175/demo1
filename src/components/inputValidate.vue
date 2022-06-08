<template>
  <input
    type="text"
    :class="['form-control', { 'is-invalid': inputRef.error }]"
    :value="inputRef.val"
    @input="onTypeInHandle"
    @blur="onBlurCheckHandle"
    v-bind="$attrs"
  >
  <div class="invalid-feedback" v-if="inputRef.error">{{ inputRef.message }}</div>
</template>

<script lang="ts">
import { defineComponent, reactive, PropType, onMounted } from 'vue'
import { emitter } from '@/components/inputValidateForm.vue'

// 自定义验证规则类型
interface IinputType {
  type: 'email' | 'phone' | 'password'
  message: string
}
export type inputTypeRules = IinputType[]

export default defineComponent({
  name: 'inputValidate',
  props: {
    inputRules: Array as PropType<inputTypeRules>,
    modelValue: String
  },
  inheritAttrs: false,
  setup (props, ctx) {
    const inputRef = reactive({
      val: props.modelValue || '',
      error: false,
      message: ''
    })

    // 自定义双向绑定
    const onTypeInHandle = (e: KeyboardEvent) => {
      const typeinValue = (e.target as HTMLInputElement).value
      inputRef.val = typeinValue
      onBlurCheckHandle()
      ctx.emit('update:modelValue', typeinValue)
    }

    // 验证input输入合法性
    const phoneReg = /^[1][3,4,5,7,8][0-9]{9}$/
    const emailReg = /^[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?$/
    const onBlurCheckHandle = (): boolean => {
      if (props.inputRules) {
        const allPassed = props.inputRules.every(rule => {
          let paddedFlag = true
          inputRef.message = rule.message

          const inputvalue = inputRef.val.trim()
          switch (rule.type) {
            case 'phone':
              paddedFlag = phoneReg.test(inputvalue)
              break
            case 'email':
              paddedFlag = emailReg.test(inputvalue)
              break
            case 'password':
              paddedFlag = inputvalue === '' && inputvalue.length < 5
              break
          }
          return paddedFlag
        })

        inputRef.error = !allPassed
        return allPassed
      }

      return true
    }

    // 发送验证事件
    onMounted(() => {
      emitter.emit('create-input-validate', onBlurCheckHandle)
    })

    return {
      inputRef,
      onBlurCheckHandle,
      onTypeInHandle
    }
  }
})
</script>
