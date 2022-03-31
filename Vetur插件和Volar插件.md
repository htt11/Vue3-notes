**`vetur` 插件和 `volar` 插件：**

ventur是一个vscode插件，主要用于对 vue 单文件组件提供语法高亮、语法支持以及语法检测；

但是，vue以及vetur对于ts的支持，并不友好。在`@vue/composition-api`这个插件 出来之前，vue2 的 ts 需要使用`vue-prototype-decorator`这个插件，来通过装饰器的模式进行模拟，但是由于不是从底层支持，因此`vue`2的`ts`使用体验可谓是一塌糊涂。

而vue3全部使用TS重写，更好的支持TS。

## 1. volar是什么？

在功能上 `volar` 和 `vetur` 是一致的，都是针对 `vue` 的插件，但是 `volar` 的功能却要强大得多，例如：在template即模板中，对TS的检测更加严格等；

可以这样说， `volar` 是 `vue3` 的配套，`vetur` 是 `vue2` 的配套，所以，建议大家在使用的时候依据自己使用的 `vue` 版本来选择；

`Volar`插件是在vscode中针对vue的插件，目前下载量低于`vetur`；

注意：

1. 使用前需要禁用`vetur`，以避免冲突；
2. 推荐使用 `css/less/scss` 作为 `<style>`的语言，因为这些基于 `vscode-css-language` 服务提供了语言支持。如果你使用 `postcss/stylus/sass` 的话，你需要安装额外的语法高亮扩展：
   1. postcss: [language-postcss](https://marketplace.visualstudio.com/items?itemName=cpylua.language-postcss).
   2. stylus: [language-stylus](https://marketplace.visualstudio.com/items?itemName=sysoev.language-stylus)
   3. sass: [Sass

## 2. volar的功能：

除了基本的 `vue` 单文件组件的语法高亮，还有许多其他功能；

另外，在vue2的template中，需要一个唯一的根标签。但在vue3中解除这个限制，在使用volar之后不会报错了；

### 2.1 编辑器快捷分割

`vue`单文件组件，按照功能，存在`template`、`script`、`style`三个根元素；

`volar`提供了一个快捷方式；

使用：在安装好`volar`之后，进入`.vue`单文件组件，写入`template`、`script`、`style`根元素，点击一下快捷方式，当前文件会被拆分为三个视窗。

### 2.2 template语法转换

`vue`默认提供了两种模板供我们使用，但是一般都会使用`html`，另外一种叫做`pug`。

相对于`html`，`pug`更偏向于`yml`那种，简洁程度特别高。

在`volar`中，为我们提供了`html`和`pug`互相转换的功能。

使用：在书写`template`之后，`template`顶部会出现一个小小的`pug`图标，点击切换；

### 2.3 ref sugar语法快捷改动支持

`ref` 语法糖 与 普通的 `script setup` 的写法 的切换；

使用：使用 `ref` 定义了变量后， 通过勾选`script setup` 上面的 `ref sugar`进行切换；

### 2.4 class references

class引用：在 `style` 中所写的 `class` 是否有被引用，引用了几次；

class追溯：在template模板中，鼠标`hover`到class名上，显示 `Follw link(cmd+click)` ，即通过`cmd` 加上鼠标点击，就可以快速的跳转到对应的样式位置；

### 2.5 props 类型检测

`vue2` 中无法实现对 `props` 的类型检测，只有当运行后才能有一个控制台的提示，同时还仅仅限于 `Number, String, Boolean, Array`；

`volar` 插件配合 `ts` 完美的实现了 `props` 的类型检测；

### 2.6 语法提示

#### 2.6.1 模板语法提示

#### 2.6.2 css module 语法提示

`css module`一般是`react`技术栈用的会比较多一些；

在`vue`里面很少使用它，因为`vue`提供了`scoped`作用域，不用担心样式冲突，直接使用预处理器会更加简单方便

#### 2.6.3 lang 语法提示

`vue`可以使用`lang`属性来选择使用的语言，比如`template`中的`html`和`pug`，`script`中的`ts`，`style`中的`scss`等
