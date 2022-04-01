# 内置组件 - teleport

在组件化开发中，我们封装一个组件A，在另一个组件B中使用：

- 那么组件A中template的元素，会被挂载到组件B中template的某个位置；
- 最终应用程序会形成一颗DOM树结构；

但某些情况下，我们希望组件不是挂载在这个组件上，可能是移动到Vue app之外的其他位置：

- 例如：移动到body元素上，或者有其他的div#app之外的元素上；
- 常见场景：创建一个包含全屏模式的组件时，我们通常希望模态框的逻辑存在于组件中，但是这样模态框的快速定位就很难通过CSS来解决，或者需要更改组件组合；
- 这个时候就可以通过teleport来完成；

**teleport是Vue提供的一个内置组件**，类似于react的Portals；

**teleport属性：**

- to：指定将其中的内容移动到的目标元素，可以使用选择器；
- disabled：是否禁用 teleport 的功能；

**teleport的使用：**

```html
<template>
  <div class="test-app">
    <h2>test-app</h2>
    <teleport to="body">
      <h2>teleport to body</h2>
    </teleport>
  </div>
</template>
```

通过查看控制台，我们会发现：

- `<teleport>...</teleport> `标签中的内容已经被移动到body元素下了；

  ```html
  <body>
    <div id="app">...</div>
    <!-- ...... -->
    <h2>teleport to body</h2>
  </body>
  ```

- 而` <h2>test-app</h2>` 这部分内容仍在当前组件中；

**teleport也可以和组件结合一起来使用：**

- 我们可以在teleport中使用组件，并且也可以传入一些数据；

```html
<template>
  <div class="my-app">
    <teleport to="body">
      <hello-world message="我是APP中的message"></hello-world>
    </teleport>
  </div>
</template>
```

查看控制台：

```js
<body>
  <!-- ...... -->
  <div>
    <h2>Hello World</h2>
	<p>App传入:我是APP中的message</p>
  </div>
</body>
```

**多个teleport的使用：**

- 如果我们将多个teleport应用到同一目标上 (to的值相同)，那么这些目标会进行合并；

```html
<template>
  <teleport to="#test">
    <h2>test title</h2>
  </teleport>
  <teleport to="#test">
    <hello-world message="我是APP中的message"></hello-world>
  </teleport>
</template>
```

查看控制台：

```html
<div id="test">
  <h2>test title</h2>
  <div>
    <h2>Hello World</h2>
    <p>App传入:我是APP中的message</p>
  </div>
</div>
```

