<script setup>
import { ref } from 'vue'
import {
  createDiscreteApi,
} from 'naive-ui'


const { message } = createDiscreteApi(
  ['message'],
)

const msg = ref(0)
const num = ref(0)
const toP = ref(0)

const handelClick = (isParent) => {
  console.log('isParent =>  ', isParent)
  if (isParent) {
    message.warning('父页面调用iframe页面中的方法')
  } else {
    message.warning('iframe内部调用')
  }
  num.value++
}

const handelToParent = () => {
  // console.log("windwo", window);
  window.parent.postMessage(++toP.value, '*');
  // window.localStorage.setItem("toParent", toP.value++)
  // localStorage.setItem('columnData', JSON.stringify(data))
}

window.addEventListener('message', onMessage)
function onMessage(e) {
  console.log('e =>  ', e)
  console.log('e xxxxx=>  ', e.data)
  try {
    const { type, payload } = e.data || {}
    switch (type) {
      case 'data':
        msg.value = payload.a
        break;
      case 'action':
        handelClick(true)
        break;

      default:
        break;
    }
  } catch (error) {

  }
}

</script>

<template>

  <div>
    <h4>iFrame body</h4>
    <div>父组件传值: {{msg}}</div>
    <div>
      动作调用:<n-button type="primary" @click="handelClick(false)">按钮{{num}}</n-button>
    </div>
    <div style="margin-top: 5px;">
      向父级通信 <n-button type="info" @click="handelToParent">+({{toP}})</n-button>
    </div>

  </div>

</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
