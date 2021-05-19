<template>
  <form class="form-controls text-start">
    <slot name="default"></slot>

    <div class="mt-4" @click.prevent="onSubmitHandle">
      <slot name="form-sbumit-btn">
        <!-- 默认button -->
        <button type="button" class="btn btn-outline-primary">提交</button>
      </slot>
    </div>
  </form>
</template>

<script lang="ts">
import { defineComponent, onUnmounted } from 'vue'
import 'bootstrap/dist/css/bootstrap.min.css'

// evenbus
import mitt from 'mitt'
export const emitter = mitt()

type inputBlurHandle = () => boolean

export default defineComponent({
  name: 'inputValidateForm',
  emits: ['validateform-submit'],
  setup (props, ctx) {
    // 接收inputvalidate发送的检测值
    let inputValidateArrys:inputBlurHandle[] = []
    const inputValidateCallBack = (inputValidateFun: inputBlurHandle | undefined) => {
      inputValidateFun && inputValidateArrys.push(inputValidateFun)
    }
    emitter.on('create-input-validate', inputValidateCallBack)

    // 发送提交按钮
    const onSubmitHandle = () => {
      const validateFormResult = inputValidateArrys.map(func => func()).every(i => i)
      ctx.emit('validateform-submit', validateFormResult)
    }

    // 取消监听
    onUnmounted(() => {
      inputValidateArrys = []
      emitter.off('create-input-validate', inputValidateCallBack)
    })

    return { onSubmitHandle }
  }
})
</script>

<style scoped lang="css">
.form-controls {
  padding: 10px;
}

.form-controls .col-md-4{
  margin-bottom: 30px;
}

.form-controls .col-md-4:last-child {
  margin-bottom: 0;
}
</style>
