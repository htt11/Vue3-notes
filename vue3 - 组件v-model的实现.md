## vue3 - 组件v-model的实现

我们可以用 v-model 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定：

例如：在input中v-model默认帮助我们完成了两件事：v-bind:value的数据绑定和@input的事件监听

```html
<input v-model="searchText" />
<!-- 等价于 -->
<input ：value="searchText" @input="searchText = $event.target.value" />
```

**同时，vue也支持在组件上使用v-model：**

- 我们会发现和input元素不同的只是属性的名称和事件触发的名称而已

```html
<my-input v-model="searchText" />
<!-- 等价于 -->
<my-input :model-value="searchText" @update:model-value="searchText = $event" />
```

### 在组件上使用v-model：

为了我们的MyInput组件可以正常的工作，这个组件内的`<input>`必须：

- 将其`value`属性绑定到一个名为 `modelValue` 的props上；
- 在其 `input` 事件被触发时，将新的值将通过自定义的 `update:modelValue` 事件抛出；

```vue
<!-- MyInput子组件 -->
<template>
  <div>
     <input :value="modelValue" @input="inputChange">
  </div>
</template>

<script>
export default ({
  props: [ 'modelValue'],
  emits: ['update:modelValue'],
  setup(props, { emit }) {
    const inputChange = (event) => {
      emit("update:modelValue", event.target.value)
    } 

    return {
      inputChange
    }
  }
})
</script>
```

```html
<!-- 父组件 -->
<my-input v-model="message" />
```

#### computed实现：

- 如果我们希望在组件内部按照双向绑定的做法去完成，可以使用计算属性的 `setter` 和 `getter` 

```vue
<!-- MyInput子组件 -->
<template>
  <div>
     <input v-model="value">
  </div>
</template>

<script>
import { computed } from 'vue';
export default ({
  props: [ 'modelValue'],
  emits: ['update:modelValue'],
  setup(props, { emit }) {
    const value = computed({
      get: () => props.modelValue,
      set: (value) => {
        emit('update:modelValue', value)
      }
    })

    return {
      value
    }
  }
})
</script>
```

#### 绑定多个属性：

默认情况下的v-model其实是绑定了 modelValue 属性和 @update:modelValue的事件；

如果我们希望绑定多个属性，可以给v-model传入属性名称作为参数，即`v-model:属性名="属性值"`

例：

```html
<my-input v-model="searchText" v-model:title="title" />
```

 v-model:title相当于做了两件事： 

- 绑定了title属性； 
- 监听了 @update:title的事件；

```vue
<!-- MyInput子组件 -->
<template>
  <div>
     <input :value="modelValue" @input="input1Change">
     <input :value="title" @input="input2Change">
  </div>
</template>

<script>
export default ({
  props: [ 'modelValue', 'title'],
  emits: ['update:modelValue', 'update:title'],
  setup(props, { emit }) {
    const input1Change = (event) => {
      emit("update:modelValue", event.target.value)
    }
    const input2Change = (event) => {
      emit("update:title", event.target.value)
    } 

    return {
      input1Change,
      input2Change
    }
  }
})
</script>
```
