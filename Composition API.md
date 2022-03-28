# Options API

在Vue2中，我们编写组件的方式是Options API： 

- Options API的一大特点就是在对应的属性中编写对应的功能模块； 
- 比如data定义数据、methods中定义方法、computed中定义计算属性、watch中监听属性改变，也包括生命 周期钩子；

但这种代码有一个很大的弊端：

- 当我们实现某个功能时，这个功能对应的代码逻辑会被拆分到各个属性中；
- 当我们组件变得更加强大、更复杂时，逻辑关注点的列表就会增长，那么同一个人功能的逻辑就会被拆分的很分散；
- 这种碎片化的代码使得理解和维护这个复杂的组件变得异常困难，并且隐藏了潜在的逻辑问题，并且当我们处理单个逻辑关注点时，需要不断的跳到相应的代码块中；
- 尤其对于那些一开始没有编写这些组件的人来说，这个组件的代码是难以阅读和理解的（阅读组件的其他人）；

# Composition API

Composition API将同一个逻辑关注点相关得代码收集到一起。

## setup函数：

setup其实就是组件的另外一个选项：

- 可以用它来替代之前所编写的大部分其他选项；
- 比如methods、computed、watch、data、生命周期等等；

### 参数：

setup函数主要有两个参数：

- 第一个参数：**props**
- 第二个参数：**context**

**`props ` 参数** 就是**父组件传递过来的属性**会被**放到props对象**中，如果我们**在setup函数中需要使用**的话，可以可以**通过props参数获取**；

- 在template中依然是可以正常去使用props中的属性，比如message；

- 在setup中使用时**将props直接作为参数**传递，不可以使用this去获取；

  - **setup中的this并不指向当前组件实例，在setup调用之前，data、computed、methods等都能有被解析，所以无法在setup中获取this；**

  ```js
  props: [ 'message'],
  setup(props) {
    console.log(props.message)
    // ......
  }
  ```

**`context` 参数** 也被称为是一个`SetupContext`，它里面包含三个属性：

- **attrs**：所有的非prop的attribute；

- **slots**：父组件传递过来的插槽（这个在以渲染函数返回时会有作用，后面会讲到）；

- **emit**：当我们组件内部需要发出事件时会用到emit（因为我们不能访问this，所以不可以通过 this.$emit发出事件）；

  ```js
  props: [ 'message'],
  emits: ['update:message']
  setup(props, { emit }) {
    const inputChange = (event) => {
      emit("update:message", event.target.value)
    } 
  }
  ```

### 返回值：

setup既然是一个函数，那么它也可以有返回值：

- setup的返回值可以在模板template中被使用，即可以通过setup的返回值来替代data选项；

- 也可以通过返回一个执行函数来代替在methods中定义的方法；

### 响应式API:

默认情况下，在setup中定义的数据变量是非响应式的，Vue并不会跟踪它的变化来引起界面的响应式操作。

如果为setup中定义的数据提供响应式的特性，可以通过如：reactive、ref 等响应式API来实现

#### `Reactive API：`

- 当我们使用reactive函数处理数据之后，数据再次被使用时就会进行依赖收集；
- 当数据发生改变时，所有收集到的依赖都是进行对应的响应式操作（如更新界面）；
- 事实上，我们编写的data选项，也是在内部交给了reactive函数将其编程响应式对象的；

reactive API对**传入的类型是有限制**的，它要求我们必须传入的是**一个对象或者数组类型**：

- 如果传入一个基本数据类型（String、Number、Boolean）会报警告；

```js
import { reactive } from 'vue';
export default ({
  setup() {
    const nums = reactive(['111', '222', '333'])
    const user = reactive({name: 'zs', age: 18})
    return {nums, user}
  }
})
```

#### `Ref API：`

如果传入一个基本数据类型时，可以使用**ref API**:

- ref 会返回一个**可变的响应式对象**，该对象作为一个 **响应式的引用** 维护着它**内部的值**，这就是ref名称的来源；
- 它内部的值是**在ref的 value 属性**中被维护的；

```bash
注意事项：
- 在模板中引入ref的值时，Vue会自动帮助我们进行解包操作，所以我们并不需要在模板中通过 ref.value 的方式 来使用； 
- 但是在 setup 函数内部，它依然是一个 ref引用， 所以对其进行操作时，我们依然需要使用 ref.value的方式；
```

**ref自动解包：**

- 模板中的解包是浅层的解包；

  ```vue
  <template>
    <div>
       <h2>{{message}}</h2>
       <h2>{{info.message.value}}</h2>
       <button @click="changeMessage">changeMessage</button>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
  export default ({
    setup() {
      const message = ref('hello world');
      const changeMessage = () => message.value = '你好呀';
      const info = { message };
  
      return {
        message,
        changeMessage,
        info
      }
    }
  })
  </script>
  ```

- 如果将ref放到reactive的属性中，那么在模板中使用时，它会自动解包；

  ```js
  const message = ref('hello world');
  const info = reactive({ message });
  ```

  ```html
  <h2>{{info.message}}</h2>  <!-- 这里不需要value -->
  ```

#### `readonly：`

通过reactive或者ref可以获取到一个响应式的对象，但是当我们传递给其他组件数据时，希望其他组件使用我们传递的内容，但是**不允许它们修改**时，就可以使用 `readonly`；

**readonly参数的类型：**

- 类型一：普通对象； 
- 类型二：reactive返回的对象； 
- 类型三：ref的对象；

**readonly的使用：**

使用规则：

- readonly返回的对象都是不允许修改的；
- 经过readonly处理的原来的对象是允许被修改的：
  - 如 const info = readonly(obj)，obj可以被修改，但info对象不允许修改；
  - 当obj被修改时，readonly返回的info对象也会被修改；
- 本质上就是readonly返回的对象的setter方法被劫持了而已；

**Reactive判断的API:**

- isProxy：检查对象是否是由 reactive 或 readonly创建的 proxy。 

- **isReactive** ：检查对象是否是由 reactive创建的响应式代理： 
  - 如果该代理是 readonly 建的，但包裹了由 reactive 创建的另一个代理，它也会返回 true； 
- **isReadonly** ：检查对象是否是由 readonly 创建的只读代理。 
- **toRaw** ：返回 reactive 或 readonly 代理的原始对象（不建议保留对原始对象的持久引用。请谨慎使用）。 
- **shallowReactive** ：创建一个响应式代理，它跟踪其自身 property 的响应性，但不执行嵌套对象的深层响应式转换 (深层还是原生对象)。 
- **shallowReadonly** ：创建一个 proxy，使其自身的 property 为只读，但不执行嵌套对象的深度只读转换（深层还是可读、可写的）

#### `toRefs/toRef：`

- 当使用ES6的结构语法对reactive返回的对象进行解构获取值，之后无论是修改结构后的变量，还是修改reactive返回的state对象，数据都不在是响应式的；

  ```js
  const state = reactive({
    name: 'zs',
    age: 18
  });
  const { name, age } = state;
  ```

- 使用**toRefs函数** 可以**将reactive返回的对象中的属性都转为ref**,那么再次解构出来的数据本身就是ref的；

  ```js
  const { name, age } = toRefs(state);
  ```

- 如果**只希望转换一个reactive对象中的属性为ref**，可以**使用toRef**方法；

  ```js
  const { name } = toRef(state, 'name');
  ```

#### ref其他的API:

**`unref`**：如果想要获取一个ref引用中的value，也可以使用unref方法：

- 如果参数是一个ref，则返回内部值，否则返回参数本身；

  ```js
  import { ref, unref } from 'vue';
  export default ({
    setup() {
      const message = ref('hello');
      console.log(unref(message))；	// hello
      return { message }
    }
  })
  ```

- 这是val = isRef(val) ? val.value : val 的语法糖； 

**`isRef`**：判断值是否是一个ref对象；

```js
import { ref, isRef } from 'vue';
export default ({
  setup() {
    const message = ref('hello');
    const message1 = 'hello1';
    console.log(isRef(message))；	// true
    console.log(isRef(message))；	// false
    return { message }
  }
})
```

**`shallowRef`**：创建一个浅层的ref对象；

**`triggerRef`** ：手动触发和 shallowRef 相关联的副作用；

```js
const info = shallowRef({name: 'why'});
const changeInfo = () => {
  // 这里的修改不是响应式的
  info.value.name = 'coderwhy';
  // 手动触发
  triggerRef(info);
}
```

**`customRef`**：

- 它需要一个工厂函数，该函数接受 track 和 trigger 函数作为参数；
- 返回一个带有get和set的对象；

案例：对双向绑定的属性进行debounce（节流）操作

```js
import { customRef } from 'vue';
export function useDebouncedRef(value, delay = 200) {
  let timeout;
  return customRef((track, trigger) => {
    return {
      get() {
        track();
        return value;
      },
      set(newValue) {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
          value = newValue;
          trigger();
        }, delay);
      }
    }
  })
}
```

```vue
<template>
  <div>
     <input v-model="message">
     <h2>{{message}}</h2>
  </div>
</template>

<script>
import { useDebouncedRef } from '../hook/useDebouncedRef';
export default ({
  setup() {
    const message = useDebouncedRef('hello');
    return {
      message
    }
  }
})
</script>
```



### computed:

当某些属性是依赖其他状态时，我们可以使用计算属性来处理：

- 在Options API中，我们是使用computed选项来完成的；
- 在Composition API中，可以在setup函数中使用computed方法来编写一个计算属性；

setup中使用computed：

- 方式一：接收一个getter函数，并为getter函数返回一个不变的ref对象；

  ```js
  const count = ref(1);
  const plusOne = computed(() => count.value + 1);
  
  console.log(plusOne.value) // 2
  plusOne.value++; // 错误
  ```

- 方式二：接收一个具有get和set的对象，返回一个可变的（可读写）ref对象；

  ```js
  const plusOne = computed({
    get: () => count.value + 1,
    set: val => {
      count.value = val -1;
    }
  })
  plusOne.value = 1;
  console.log(count.value) // 0
  ```

### watch:

在Options API中，可以通过watch选项来侦听data或props的数据变化；

在Composition API中，我们可以使用watchEffect和watch来完成响应式数据的侦听：

- **watchEffect**用于自动收集响应式数据的依赖；
- **watch**需要手动指定侦听的数据源；

#### watchEffect：

如果希望 侦听到某些响应式数据变化时去执行某些操作，就可以使用`watchEffect`：

- watchEffect传入的函数会立即被执行一次，并且在执行的过程中会收集依赖；
- 只有收集的依赖发生变化时，watchEffect传入的函数才会再次执行；

##### 停止侦听：

当 `watchEffect` 在组件的 `setup()函数`或`生命周期钩子`被调用时，侦听器会被链接到该组件的生命周期，并在组件卸载时自动停止；

在一些情况下，也可以显式调用返回值以停止侦听：

```js
// age达到20的时候就停止侦听
const stop = watchEffect(() => {
  console.log("watchEffect执行~", age.value)
})
const changeAge = () => {
  age.value ++;
  if (age.value > 20) {
    stop();
  }
}
```

##### 清除副作用：

什么是副作用：

- 比如在开发中我们需要侦听函数中执行网络请求，但是在网络请求还没有达到的时候，如果我们停止了侦听器或者侦听器侦听函数被再次执行了；
- 那么上一次的网络请求应该取消掉，这个时候就可以清除上一次的副作用；

在给watchEffect传入的函数被回调时，其实可以获取到一个参数：`onInvalidate`，用来注册清理失效时的回调：

- 当**副作用即将重新执行** 或者 **侦听器被停止** 时会执行该函数传入的回调函数；
- 我们可以在传入的回调函数中，执行一些清除工作；

```js
const stop = watchEffect((onInvalidate) => {
  const timer = setTimeout(() => {
    console.log('网络请求成功~');
  }, 2000);
  onInvalidate(() => {
    // 在这个函数中清除额外的副作用
    clearTimeout(timer);
  })
})
```

##### 执行时机：

默认情况下，组件的更新会在副作用执行之前：

```vue
<template>
  <div>
    <h2 ref="title">哈哈哈</h2>
  </div>
</template>
<script>
import { ref, watchEffect } from 'vue';
export default {
  setup() {
    const title = ref(null);
    watchEffect(() => {
      console.log(title.value);
    })
    return {
      title
    }
  }
}
</script>
```

执行上面例子会发现结果打印了两次：

- 第一次打印为`null`，因为setup函数在执行时会立即执行传入的副作用函数，这时DOM并没有挂载，所以打印为null；
- 第二次打印为 `<h2></h2>`，因为DOM挂载时会给title的ref对象赋值新的值，副作用函数会再次执行，打印出来对应的元素；

**调整watchEffect的执行时机：**

如果我们希望在上面的例子中第一次就打印除对应的元素呢？

- 这就需要改变副作用函数的执行时机；

- 它的默认值是pre，它会在元素挂载 或 更新之前执行；

  ```js
  // 注意：这也将推迟副作用的初始运行，直到组件的首次渲染完成。
  watchEffect(
    () => {
      /* ... */
    },
    {
      flush: 'post'
    }
  )
  ```

- flush 选项还接受 sync，这将强制效果始终同步触发。然而，这是低效的，应该很少需要；

#### watch：

watch的API完全等同于组件watch选项的property：

- watch需要侦听特定的数据源，并在回调函数中执行副作用；
- 默认情况下它是惰性的，只有当被侦听的源发生变化时才会执行回调；

与watchEffect相比，watch允许：

- 懒执行副作用（第一次不会直接执行）；
- 更具体的说明当哪些状态发生改变时，触发侦听器的执行；
- 访问侦听状态前后的值；

watch 的选项：

- deep：如果我们希望侦听一个深层的侦听，那么依然需要设置 deep 为true；
- immediate：立即执行；

##### 侦听单个数据源：

watch侦听函数的数据源有两种类型：

- 一个getter函数：但是该getter函数必须引用可响应式的对象（比如reactive 或者ref）；

  ```js
  const state = reactive({ name: 'zs', age: 18 });
  watch(() => state.name, (newVal, oldVal) => {
    console.log(newVal, oldVal);
  })
  const changeName = () => {
    state.name = 'ls';
  }
  ```

- 直接写入一个可响应式的对象，reactive或者ref（常用ref）；

  ```js
  const name = ref('zs');
  watch(name, (newVal, oldVal) => {
    console.log(newVal, oldVal);
  })
  const changeName = () => {
    name.value = 'ls';
  }
  ```

##### 侦听多个数据源：

侦听器还可以使用数组同时侦听多个源：

```js
const name = ref('zs');
const age = ref(18);
watch([name, age], (newVal, oldVal) => {
  console.log(newVal, oldVal);
})
const changeName = () => {
  name.value = 'ls';
}
```

##### 侦听响应式对象：

如果我们希望侦听一个数组或者对象，那么可以使用一个getter函数，并且对可响应对象进行解构：

```js
const names = reactive(['aa', 'bb', 'cc']);
watch(() => [...names], (newVal, oldVal) => {
  console.log(newVal, oldVal);
})
const changeName = () => {
  name.push('dd');
}
```



### 生命周期钩子：

setup中使用生命周期函数：直接导入的 onX 函数注册生命周期钩子；

| 选项式 API    | Hook inside setup |
| ------------- | ----------------- |
| beforeCreate  | 使用 `setup()`    |
| created       | 使用 `setup()`    |
| beforeMount   | onBeforeMount     |
| mounted       | onMounted         |
| beforeUpdate  | onBeforeUpdate    |
| updated       | onUpdated         |
| beforeUnmount | onBeforeUnmount   |
| unmounted     | onUnmounted       |
| activated     | onActivated       |
| deactivated   | onDeactivated     |

注：因为`setup`是围绕`beforeCreate`和`created`生命周期钩子运行的，所以不需要显式地定义它们，即需要在这些钩子中写的代码都应该直接在`setup`函数中编写。

### Provide / Inject：

#### provide函数：

- 可以通过 provide 方法来定义每个 Property； 
- provide可以传入两个参数： 
  - name：提供的属性名称
  - value：提供的属性值

```js
import { provide } from 'vue';
// ....
setup() {
  let counter = 100;
  provide('counter', counter);
}
```

#### inject 函数：

- 在后代组件中通过 inject 注入需要的属性和对应的值；
- inject可以传入两个参数：
  - 要 inject 的 property 的 name
  - 默认值

```js
import { inject } from 'vue';
// ...
setup() {
  const counter = inject('counter');
  return { counter }
}
```

#### 数据的响应式：

为了增加 provide 值和 inject 值之间的响应性，我们可以在 provide 值时使用 ref 和 reactive;

```js
let counter = 100;
let info = reactive({
  name: 'zs',
  age: 18
})
provide('counter', counter);
provide('info', info);
```

如果需要修改可响应的数据，最好是在数据提供的位置来修改：

- 可以将修改方法进行共享，在后代组件中进行调用；

```js
const changeInfo = () => {
  info.name = 'ls';
}
provide('changeInfo', changeInfo);
```







