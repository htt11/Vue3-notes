## nextTick：

将回调推迟到下一个DOM更新周期之后执行。在更改了一些数据以等待DOM更新后立即使用它。

```
import { createApp, nextTick } from 'vue';
const app = createApp({
  setup() {
    const message = ref('hello');
    const changeMessage = saync newMessage => {
      message.value = newMessage;
      await nextTick()
      console.log('now Dom is updated')
    }
  }
})
```

案例：点击一个按钮，修改在h2中显示的message；message被修改后，获取h2的高度；

实现方式：

- 方式一：点击按钮后立即获取到h2的高度；（错误做法）
- 方式二：在updated生命周期函数中获取h2的高度；（但是其他数据更新，也会执行该操作）
- 方式三：使用nexttick函数；

```html
<h2 class="title" ref="titleRef">{{message}}</h2>
<button @click="addMessageContent">添加内容</button>
```

```js
import { ref, nextTick } from "vue";
// ...
setup() {
  const message = ref("");
  const titleRef = ref(null);
  const addMessageContent = () => {
    message.value += "哈哈哈哈哈哈哈哈哈哈"

    // 更新DOM
    nextTick(() => {
      console.log(titleRef.value.offsetHeight)
    })
  }
  return {
    message,
    titleRef,
    addMessageContent
  }
}
```

