# Vuex的状态管理：

## 状态管理：

**什么是状态管理：**

- 在开发中，应用程序需要处理各种各样的数据，
- 这些数据需要保存在我们之前应用程序的某个位置，
- 对于这些数据的管理我们就称之为状态管理；

**组件的状态管理：**

- 在Vue开发中，我们使用组件化的开发方式；
- 而在组件中我们定义data或者在setup中返回使用的数据，这些数据我们称之为 state；
- 在template 中我们可以使用这些数据，模板最终会被渲染成DOM，我们称之为 View；
- 在模块中我们会产生一些行为事件，处理这些行为事件时，可能会修改state，这些行为事件我们称之为 actions；

**复杂的状态管理：**

- JS开发的应用程序，已经变得越来越复杂了：
  - JS需要管理的状态越来越多，越来越复杂；
  - 这些状态包括服务器返回的数据、缓存数据、用户操作产生的数据等等；
  - 也包括一些UI的状态，比如某些元素是否被选中、是否显示加载动效、当前分页；

- 当我们的应用遇到**多个组件共享状态**时，单向数据流的简洁性很容易被破坏：
  - 多个视图依赖于同一状态；
  - 来自不同视图的行为需要变更同一状态；

- 我们是否可以通过组件数据的传递来完成呢？
  - 对于一些简单的状态，确实可以通过props的传递或者Provide的方式来共享状态；
  - 但是对于复杂的状态管理来说，显然单纯通过传递和共享的方式不足以解决问题，比如：兄弟组件如何共享数据；

**Vuex的状态管理：**

- 管理不断变化的state本身是非常困难的：
  - 状态之间相互会存在依赖，一个状态的变化会引起另一个状态的变化，View页面也可能会引起状态的变化；
  - 当应用程序复杂时，state在什么时候、什么原因发生了变化，发生了什么变化，会变得非常难以控制；

- 因此，将组件的内部状态抽离出来，以一个全局单例的方式来管理：
  - 在这种模式下，组件树构成了一个巨大的“视图View”；
  - 不管在树的哪个位置，任何组件都能获取状态或者触发行为；
  - 通过定义和隔离状态管理中的各个概念，并通过强制性的规则维护视图和状态间的独立性，代码会变得更加结构化和易于维护、跟踪；

- 这就说Vuex的基本思想，它借鉴了Flux、Redux、Elm(纯函数语言，redux有借鉴它的思想)；

## Vuex的安装：

```bash
npm install vuex
# 或
npm install vuex@next	# 指定版本
```

## Vuex的使用：

### 创建Store：

每一个Vuex应用的核心就是store（仓库）；

- store本质上是一个容器，它包含应用中大部分的状态（state）；

**Vuex和单纯的全局对象的区别：**

- Vuex的状态存储是响应式的：
  - 当Vue组件从store中读取状态时，若store中的状态发生变化，那么相应的组件也会被更新；
- 不能直接改变store中的状态：
  - 改变store中的状态的唯一途径就是通过显式提交(commit) mutation；
  - 这样使得我们可以方便的跟踪每一个状态的变化，从而让我们能通过一些工具帮助我们更好的管理应用的状态；

### 组件中使用 store：

#### 使用方式：

- 在模板中使用；
- 在 options api 中使用，比如 computed；
- 在 setup 中使用；

#### 单一状态树：

Vuex中使用单一状态树：

- 用一个对象就包含了全部的应用层级状；
- 采用的是SSOT、Single Source of Truth，也可以翻译成单一数据源；
- 这也意味着，每个应用将仅仅包含一个 store 实例；
- 单状态树和模块化并不冲突；

单一状态树的优势：

- 如果你的状态信息是保存到多个Store对象中的，那么之后的管理和维护等都会变得特别困难；
- 所有Vuex也使用了单一状态树来管理应用层级的全部状态；
- 单一状态树能够让我们最直接的方式某个状态的片段，而且在之后的维护和调试过程中，也非常方便管理和维护；

### state的使用：

#### 在computed中使用：

利用计算属性可以使用store获取组件状态；

```js
computed: {
 counter() {
   return this.$store.state.counter;
 }
}
```

但是，如果有多个状态都需要获取的话，可以使用mapState的辅助函数：

- mapState的方式一：对象类型；

  ```js 
  import { mapState } from 'vuex';
  export default {
    computed: mapState({
      const state => state.count;
      // 传字符串参数 'count' 等同于 `state => state.count`
      countAlias: 'count',
      countPlusLocalState (state) {
        return state.count + this.localCount
      }
    })
  }
  ```

- mapState的方式二：数组类型；

  - 当映射的计算属性的名称与state的子节点名称相同时，也可以传一个字符串数组；

  ```js
  computed: mapState([
    // 映射 this.count 为 store.state.count
    'count'
  ])
  ```

也可以使用展开运算符和原有的computed混合在一起；

#### 在setup中使用：

在setup中如果获取单个状态：

- 通过useStore拿到store后去获取某个状态；

使用mapState获取多个状态：

- 默认情况下，Vuex并没有提供非常方便的使用mapState的方式；

- 可以进行一个函数的封装；

  ```js
  import { useStore, mapState } from 'vuex';
  import { computed } from 'vue';
  
  export function useState(mapper) {
    const store = useStore();
    const stateFns = mapState(mapper);
    const state = {};
    Object.keys(stateFns).forEach(fnKey => {
      state[fnKey] = computed(stateFns[fnKey].bind({$store: store}))
    })
    return state;
  }
  ```

  ```js
  setup() {
    const state = useState({
      name: state => state.name,
      age: state => state.age,
      height: state => state.height
    })
    
    return { ...state }
  }
  ```

### getters的使用：

如果有多个属性需要用到此属性，可以使用getters；

#### 参数：

- 第一个参数：state
- 第二个参数：可选，getter 也可以接受其他 getters 作为

#### 返回函数：

getters的函数本身，可以返回一个函数，所以在使用的地方相当于可以调用这个函数：

```js
getters： {
  totalPrice(state) {
    return (price) => {
      let totalPrice = 0;
      // ...
      return totalPrice
    }
  }
}
```

#### mapGetters的辅助函数：

在computed中使用：

```js
computed: {
  // ...mapGetters(["totalPrice", "myName"]),
  ...mapGetters({
    finalPrice: "totalPrice",
    finalName: "myName"
  }),
}
```

在setup中使用：

```js
import { useStore, mapGetters } from 'vuex';
import { computed } from 'vue';

export function useGetters(mapper) {
  const store = useStore();
  const stateFns = mapGetters(mapper);
  const state = {};
  Object.keys(stateFns).forEach(fnKey => {
    state[fnKey] = computed(stateFns[fnKey].bind({$store: store}))
  })
  return state;
}
```

### mutation的使用：

更改 Vuex 的 store 中的state状态的唯一方法是提交 mutation；

```js
mutations: {
 increment(state) {
   state.counter++;
 },
 decrement(state) {
   state.counter--;
 }
}
```

```js
store.commit('increment')
```

#### 参数：

- state
- payload

```js
increment(state, n) {
  state.counter += n;
}
```

```js
store.commit('increment', 10)
```

大多数情况下，payload应该是一个对象，这样可以包含多个字段；

```js
increment(state, payload) {
  state.counter += payload.count;
}
```

```js
store.commit('increment', {
  count: 10
})
```

#### 对象风格的提交方式：

提交 mutation 的另一种方式是直接使用包含 `type` 属性的对象：

```js
store.commit({
  type: 'increment',
  amount: 10
})
```

#### Mutation常量类型：

使用常量替代 mutation 事件类型在各种 Flux 实现中是很常见的模式；

例：

- 定义常量：

  ```JS
  // motation-type.js
  export const ADD_NUMBER = 'IN_CREMENT';
  ```

- 定义mutation：

  ```JS
  [ADD_NUMBER](state, payload) {
    state.counter += payload.count;
  }
  ```

- 提交mutation：

  ```JS
  store.commit({
    type: ADD_NUMBER,
    amount: 10
  })
  ```

#### mapMutations辅助函数:

我们也可以借助辅助函数，帮助我们快速映射到对应的方法中：

```js
methods: {
  ...mapMutations({
    addNumber: ADD_NUMBER
  }),
  ...mapMutations(['increment', 'decrement']),
}
```

**在setup中使用：**

```js
const mutations = mapMutations(['increment', 'decreament']);
const mutations2 = mapMutations([
  addNumber: ADD_NUMBER
])
```

#### mutation重要原则：

注：**mutation 必须是同步函数**：

- 因为devtool工具会记录mutation的日记；
- 每一条mutation被记录，devtools都需要捕捉到前一状态和后一状态的快照；
- 但是在mutation中执行异步操作，就无法跟踪到数据的变化；
- 所以Vuex的重要原则中要求 mutation必须是同步函数；

### action的使用：

Action类似于mutation，不同在于： 

- Action提交的是mutation，而不是直接变更状态； 
- Action可以包含任意异步操作；

#### 参数：

- context

```js
mutations: {
  increment(state) {
    state.counter++;
  }
},
actions: {
 increment(context) {
   context.commit('increment')
 }
}
```

context是一个和store实例均有相同方法和属性的context对象；

可以从中获取到commit方法来提交一个mutation，或者通过 context.state 和 context.getters来获取state和getters；

但是为什么它不是store对象呢？在Modules时会有解释；

#### 分发操作：

如何使用action呢？ --进行action的分发

- 分发使用的是 store 上的 dispatch 函数：

```js
add() {
  this.$store.dispatch('increment');
}
```

- 携带参数：

```js
add() {
  this.$store.dispatch('increment', { count: 100 });
}
```

- 以对象形式进行分发；

```js
add() {
  this.$store.dispatch({
    type: increment',
    count: 100 
  });
}
```

#### mapActions辅助函数：

对象类型写法：

```js
methods: {
  ...mapActions(['increment', 'decrement']),
  ...mapActions({
    add: 'increment',
    sub: 'decrement'
  })
}
```

数组类型写法：

```js
const actions1 = mapActions(['decrement']);
const actions2 = mapActions({
  add: 'increment',
  sub: 'decrement'
})
```

#### 异步操作：

action 通常是异步的，那么如何让知道 action 什么时候结束呢？

- 通过让action返回Promise，在Promise的then中处理完成后的操作；

```js
actions: {
  increment(context) {
    return new Promise((resolve) => {
      setTimeout(() => {
        context.commit("increment")
        resolve("异步完成")
      }, 1000)
    })
  }
}
```

```js
const store = useStore();
const increment = () => {
  store.increment = () => {
    store.dispatch("increment").then(res => {
      console.log(res, "异步完成");
    })
  }
}
```

### module的使用：

#### 什么是module？

- 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象，当应用变得非常复杂时，store对象就有可能变得非常臃肿；
- 为了解决以上问题，Vuex允许我们将 store 分割成模块（module）；
- 每个模块拥有自己的 state、mutation、action、getter，甚至时嵌套子模块；

```js
const moduleA = {
  state: () => ({}),
  getters: {},
  mutations: {},
  actions: {}
}
const moduleB = {
  state: () => ({}),
  mutations: {},
  actions: {}
}
```

```JS
const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})
store.state.a;	// moduleA的状态
store.state.b;	// moduleB的状态
```

#### 局部状态：

对于模块内部的 mutation 和 getter，接收的第一个参数是：模块的局部状态对象

```js
const moduleA = {
  state: () => ({ count: 0}),
  mutations: {
    increment(state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count ++;
    }
  },
  getters: {
    doubleCount(state) {
      return state.count * 2;
    }
  }
}
```

对于模块内部的 action，局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`：

```js
const moduleA = {
  // ...
  actions: {
    incrementAction({state, commit, rootState}) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```

对于模块内部的 getter，根节点状态会作为第三个参数暴露出来：

```js
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count;
    }
  }
}
```

#### 命名空间：

默认情况下，模块内部的 action 和 mutation 仍然是**注册在全局的命名空间**中的：

- 这样使得多个模块能够对同一个 action 或 mutation 作出响应；
- getter 同样也默认注册在全局命名空间；
- 注：不要在不同的、无命名空间的模块中定义两个相同的getter从而导致错误；

如果我们希望模块具有更高的封装度和复用性，可以添加 `namespaced: true` 的方式使其成为带命名空间的模块：

- 当模块注册后，它的所有 getter、action、mutation 都会自动根据模块注册的路径调整命名；
- 启用了命名空间的 getter 和 action 会收到局部化的 `getter`，`dispatch` 和 `commit`；

```js
const modules = {
  account: {
    namespace: true,
    state: () => ({ ... }),
    getters: {
      isAdmin () { ... } // -> getters['account/isAdmin']
    },
    actions: {
      login () { ... } // -> dispatch('account/login')
    },
    mutations: {
      login () { ... } // -> commit('account/login')
    },
    // 嵌套模块
    modules: {
      // 继承父模块的命名空间
      myPage: {
        state: () => ({ ... }),
        getters: {
          profile () { ... } // -> getters['account/profile']
        }
      },
      // 进一步嵌套命名空间
      ports: {
        namespace: true,
        state: () => ({ ... }),
        getters: {
          popular () { ... } // -> getters['account/posts/popular']
        }
      }
    }
  }
}
```

##### 在带命名空间的模块内访问全局内容：

如果希望使用全局的 state 和 getter，`rootState` 和 `rootGetters` 会作为第三个第四参数传入 getter，也会通过 `context` 对象的属性传入 action；

```js
modules: {
  foo: {
    namespaced: true,
    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    }
  }
}  
```

若需要在全局命名空间分发 action 或提交 mutation，将 `{ root: true }` 作为第三参数传给 `dispatch` 或 `commit` ；

```js
modules: {
  foo: {
    namespaced: true,
    // ...
    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
} 
```

##### 在带命名空间的模块注册全局 action：

若需要在带命名空间的模块注册全局 action，你可添加 `root: true` ，并将这个action 的定义放在函数 `handler` 中：

```js
modules: {
  foo: {
    namespaced: true,
    // ...
    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction: {
        root: true,
        handler (namespacedContext, payload) { ... }  // -> 'someAction'
      }
    }
  }
} 
```

##### 带命名空间的绑定函数:

辅助函数的使用方法：

- 方式一：通过完整的模块空间名称来查找；

  ```js
  computed: {
    ...mapState({
      a: state => state.some.nested.module.a,
      b: state => state.some.nested.module.b
    })
  },
  methods: {
    ...mapActions([
      'some/nested/module/foo', // -> this['some/nested/module/foo']()
      'some/nested/module/bar' // -> this['some/nested/module/bar']()
    ])
  }
  ```

- 方式二：第一个参数传入模块空间名称，后面写上要使用的属性；

  - 这样所有绑定都会自动将该模块作为上下文

  ```js
  computed: {
    ...mapState('some/nested/module', {
      a: state => state.a
    })
  },
  methods: {
    ...mapActions('some/nested/module', [
      'foo'	 // -> this.foo()
    ])
  }
  ```

- 方式三：通过createNamespacedHelpers 生成一个模块的辅助函数；

  - 对象里有新的绑定在给定命名空间值上的组件绑定辅助函数；

  ```js
  import { createNamespacedHelpers } from 'vuex'
  const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')
  
  export default {
    computed: {
      // 在 `some/nested/module`中查找
      ...mapState({
        a: state => state.a
      })
    },
    methods: {
      ...mapActions([ 'foo' ])
    }
  }
  ```

