# 渲染函数 - h函数：

Vue推荐在绝大多数情况下使用模板来创建HTML，但在一些情景中，如果真的需要JavaScript的完全编程的能力，这个时候可以使用渲染函数，它比模板更接近编译器；

- Vue在生成真实的DOM之前，会将我们的节点转换为VNode，而VNode组合在一起形成树结构，就是虚拟DOM（VDom）;
- 事实上，我们之前编写的template中的HTML最终也是使用渲染函数生成对应的VNode；
- 那么，如果想充分的使用JavaScript的编程能力，就可以自己来编写 createVNode 函数，生成对应的 VNode；
- 这就需要使用 `h()` 函数来实现。

## h() 函数：

`h()` 函数是一个用于创建vnode的一个函数；

其实更准确的命名是 `createVNode()` 函数，但是为了简便在Vue将之简化为 `h()` 函数；

### h() 参数：

- tag
  - {String | Object | Function}，必需值，如：div
  - 一个 HTML 标签名、一个组件、一个异步组件、或一个函数式组件
- props
  - {Object}，可选值
  - 与attribute、prop 和事件相对应的对象，会在模板中用到
- children
  - {String | Array | Object}，可选值
  - 子 VNodes, 使用 `h()` 构建, 或使用字符串获取 "文本 VNode" 或者有插槽的对象

```js
h(
  'div',
  {},
  [
    'Some text comes first.',
    h('h1', 'A headline'),
    h(MyComponent, {
      someProp: 'foobar'
    })
  ]
)
```

注意事项：

- 如果没有props，那么通常可以将children作为第二个参数传入； 
- 如果会产生歧义，可以将null作为第二个参数传入，将children作为第三个参数传入；

### h() 函数的基本使用：

h函数可以在两个地方使用： 

- render函数选项中； 

  ```js
  import { h } from 'vue';
  export default {
    render() {
      return h('div', {class: "app"}, "Hello")
    }
  }
  ```

- setup函数选项中（setup本身需要是一个函数类型，函数再返回h函数创建的VNode）；

  ```js
  import { h } from 'vue';
  export default {
    setup() {
      return () => h('div', {class: "app"}, "Hello")
    }
  }
  ```

## jsx的babel配置：

如果我们希望在项目中使用jsx，那么我们需要添加对jsx的支持： 

- jsx我们通常会通过Babel来进行转换（React编写的jsx就是通过babel转换的）； 
- 对于Vue来说，我们只需要在Babel中配置对应的插件即可；

**Babel 插件的使用：**

- 安装Babel支持Vue的jsx插件：

  ```bash
  npm install @vue/babel-plugin-jsx -D
  ```

- 在babel.config.js配置文件中配置插件：

  ```js
  modules.exports = {
    presets: [
      '@vue/cli-plugin-babel/preset'
    ],
    plugins: [
      '@vue/babel-plugin-jsx'
    ]
  }
  ```

- 使用：

  ```js
  export default {
    render() {
      return (
        <div class="app">Hello</div>
      )
    }
  }
  ```

  



