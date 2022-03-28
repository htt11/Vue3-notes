## Mixin

### 基础：

- Mixin 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能；

- 一个Mixin对象可以包含任何组件选项； 
- 当组件使用Mixin对象时，所有Mixin对象的选项将被 混合 进入该组件本身的选项中；

例：

```js
// sayHello.js
const sayHelloMixin = {
  created() {
    this.sayHello();
  },
  methods: {
    sayHello() {
      console.log("hello")
    }
  }
}
export default sayHelloMixin;
```

在组件中引入：

```js
import sayHelloMixin from '../mixins/sayHello';

export default {
  mixins: [sayHelloMixin]
}
```

### 选项合并：

当组件和 mixin 对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

**默认规则：**

- **情况一：如果是data函数的返回值对象**
  - 返回值对象默认情况下会**进行合并**； 
  - 如果data返回值对象的属性发生了冲突，那么会**保留组件自身的数据**；
- **情况二：如何生命周期钩子函数** 
  - 生命周期的钩子函数会被**合并到数组**中，都会被调用； 
  - mixin 对象的钩子将在组件自身钩子**之前**调用;
- **情况三：值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象**
  - 比如都有**methods选项**，并且都定义了方法，那么**它们都会生效**； 
  - 但是如果**对象的key相同**，那么**会取组件对象的键值对**；

**自定义规则：**

如果想让某个自定义选项以自定义逻辑进行合并，可以在 `app.config.optionMergeStrategies` 中添加一个函数。

合并策略接收在父实例和子实例上定义的该选项的值，分别作为第一个和第二个参数；

### 全局混入Mixin：

如果组件中的某些选项，是所有的组件都需要拥有的，那么这个时候我们可以使用全局的mixin： 

- 全局的Mixin可以使用 **应用app的方法 mixin** 来完成注册；
- 一旦注册，那么**全局混入的选项将会影响每一个组件**；

```js
const app = createApp(App);
app.mixin({
  created() {
    console.log("global mixin created")
  }
})
app.mount("#app");
```

### 不足：

在 Vue 2 中，mixin 是将部分组件逻辑抽象成可重用块的主要工具。但是，他们有几个问题：

- Mixin 很容易发生冲突：因为每个 mixin 的 property 都被合并到同一个组件中，所以为了避免 property 名冲突，你仍然需要了解其他每个特性。
- 可重用性是有限的：我们不能向 mixin 传递任何参数来改变它的逻辑，这降低了它们在抽象逻辑方面的灵活性。

在开发中extends用的非常少，在Vue2中比较推荐大家使用Mixin，而在Vue3中推荐使用Composition API。

## extends

另外一个类似于Mixin的方式是通过extends属性：

- 允许声明扩展另外一个组件，类似于Mixins；
