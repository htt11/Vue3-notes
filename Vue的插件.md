# Vue的插件：

**通常我们向Vue全局添加一些功能时，会采用插件的模式，它有两种编写方式：**

- 对象类型：一个对象，但是必须包含一个install的函数，该函数会在安装插件时执行；

  ```js
  export default {
    name: 'why',
    install(app, options) {
      console.log('插件被安装', app, options);
      console.log(this.name);
    }
  }
  ```

- 函数类型：一个function，这个函数会在安装插件时自动执行；

  ```js
  export default function(app, options) {
    console.log('插件被安装', app, options);
  }
  ```

**插件可以完成的功能没有限制，例如：**

- 添加全局方法或者property，通过把它们添加到 config.globalProperties 上实现；
- 添加全局资源：指令/过滤器/过渡等；
- 通过全局 minxin 来添加一些组件选项；
- 一个库，提供自己的API，同时提供上面提到的一个或多个功能；
