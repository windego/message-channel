# 如何通过方式iframe实现父子页面通信(MessageChannel)

## 问题背景？

针对支持平台现有架构设计限制等原因，在考虑如何只使用现有逻辑而重写页面。

初步研究了通过iframe方式引入页面并实现父子页面通信。

## MessageChannel

Channel Messaging API 的**MessageChannel** 接口允许我们创建一个新的消息通道，并通过它的两个MessagePort 属性发送数据。

## 上代码

### 1.页面结构

通过如下方式实现，目的增加项目可扩展性。

父页面：原生页面
子页面：vue3
2.父→子 通信

通过iframe方式引入子页面

```html
<iframe name="iframe" src="http://172.20.10.2:5173" width="480" height="320"></iframe>
<button id="btn">发送数据</button>
```

初始化MessageChange，当iframe页面load后，发送初始化数据，可以对postMessage参数封装分类，可实现发送数据，调用子页面方法等

```js
  const channel = new MessageChannel()
  const iframe = document.querySelector('iframe')

  // Wait for the iframe to load
  iframe.addEventListener('load', onLoad)

  // Post message to the iframe
  function postMsg(type, payload) {
    iframe.contentWindow.postMessage({ type, payload }, '*')
  }
  
  function onLoad() {
    console.log("onLoad");
    postMsg('data', { a: 111 })
  }
```

子页面监听message

```js
window.addEventListener('message', function onMessage(e) {
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
})
```

如此实现了父页面向子页面传递初始化数据。

后续发送更多数据，通过按钮事件

```js
btn.onclick = function () {
   postMsg('data', { msg:"发送其他数据" })
   postMsg('action', { name: 'ok' })
}
```

### 3.子→父 通信

子页面通过parent.postMessage 想父页面发送数据

```js
window.parent.postMessage("向父页面发送数据", '*');
```

父页面接收数据

```js
window.addEventListener('message', function (e) {
    console.log("父页面接受iframe消息", e);
    // 接收数据并处理
    // ...
    // 接受到父文档的消息后，对于同源页面可以使用localStorage广播给其他的页面
    // localStorage.setItem('info', e.data)
 })
 //其他同源页面可以通过监听storage事件，接收消息
 window.addEventListener('storage', function (ev) {
    console.log('ev =>  ', ev)
    if (ev.key == 'info') {
     //处理数据
     //...
    }
 })
```

## 三、说结论

通过iframe可以实现vue2 与内嵌vue3页面的双向数据通信。

微前端框架 qiankun 无法实现vue2与vue3的混用项目。是否可以搞一套一种基于iframe的微前端框架，解决目前项目的一些痛点？

## 四、TODO

研究一下 擎天
