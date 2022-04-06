# Vue-router：

## 认识路由：

路由其实是网络工程中的一个术语：

- 在架构一个网络时，非常重要的两个设备就是路由器和交换机；
- 事实上，路由器主要维护的是一个映射表，映射表会决定数据的流向；

路由的概念在软件工程中出现，最早是在后端路由中实现的；

## Web的发展阶段：

- 后端路由阶段：
- 前后端分离阶段：
- 单页面富应用（SPA）；

### 后端路由阶段：

早期的网站开发整个HTML页面是由服务器来渲染的：

- 服务器直接生产好对应的HTML页面，返回给客户端进行展示；

但是，在一个网站中，服务器是如何处理这么多页面的呢？

- 每个页面有自己对应的网址，也就是**URL**;
- URL会发送到服务器，服务器会通过正则对该URL进行匹配，并且最后交给一个Controller进行处理；
- Controller进行各种处理，最终生成HTML或者数据，返回给前端；

上面这种操作，就是**后端路由**：

- 当我们页面中需要请求不同的路径内容时，交给服务器来进行处理，服务器渲染好整个页面，并且将页面返回给客户端；
- 这种情况下渲染好的页面，不需要单独加载任何的js和css，可以直接交给浏览器展示，这样有利于SEO的优化；

**后端路由的缺点：**

- 一种情况是：整个页面的模板由后端人员来编写的维护的；
- 另一种情况是：前端开发人员如果要开发页面，需要通过PHP和Java等语言来编写页面代码；
- 而且通常情况下HTML代码和数据以及对应的逻辑会混到一起，编写和维护都是非常糟糕的事情；

### 前后端分离阶段：

**前端渲染的理解：**

- 每次请求涉及到的静态资源都会从静态资源服务器获取，这些资源包括 HTML+CSS+JS，然后在前端对这些请求回来的资源进行渲染；
- 需要注意的是，客户端的每一次请求，都是从静态资源服务器请求文件；
- 和之前的后端路由不同的是，这时后端只是负责提供API了；

**前后端分离阶段：**

- 随着Ajax的出现，有了前后端分离的开发模式；
- 后端只提供API来返回数据，前端通过Ajax获取数据，并且可以通过JavaScript将数据渲染到页面中；
- 这样做最大的优点是：前后端责任的清晰，后端专注于数据上，前端专注于交互和可视化上；
- 并且当移动端 (iOS/Android) 出现后，后端不需要进行任何处理，仍然使用之前的一套API即可；
- 目前比较少的网站采用这种模式开发（jQuery开发面模式）；

前端路由是如何做到URL和内容进行映射呢？ -- 监听URL的改变；

#### URL的hash：

- URL的hash也就是锚点 (#)，本质上是改变 window.location 的href属性；

- 我们可以通过直接赋值location.hash来改变href，但是页面不刷新；

  ```html
  <div>
    <a href="#/home">home</a>
    <a href="#/about">about</a>
    <div class="router-view"></div>
  </div>
  <script>
    const contentEl = document.querySelector('.content');
    window.addEventListener("hashchange", () => {
      switch(location.hash) {
        case "#/home":
          contentEl.innerHTML = "Home";
          break;
        case "#/about":
          contentEl.innerHTML = "About";
          break;
        default:
          contentEl.innerHTML = "Default";
      }
    })
  </script>
  ```

- hash的优势就是兼容性更好，在老版本的IE中都可以运行，但是缺陷是有一个 #，显得不像一个真实的路径；

#### HTML5的History：

history接口是HTML5新增的, 它有六种模式改变URL而不刷新页面：

- replaceState：替换原来的路径；
- pushState：使用新的路径； 
- popState：路径的回退； 
- go：向前或向后改变路径； 
- forward：向前改变路径； 
- back：向后改变路径；

```js
// 1.获取router-view
const routerViewEl = document.querySelector(".router-view");
// 2.监听所有的a元素
const aEls = document.getElementsByTagName("a");
for (let aEl of aEls) {
  aEl.addEventListener("click", e => {
    e.preventDefault();		// 防止链接打开URL
    const href = aEl.getAttribute("href");
    history.pushState({}, "", href);
    // history.replaceState({}, "", href);
    historyChange();
  })
}
// 3.监听popstate和go操作
window.addEventLister("popstate", historyChange);
window.addEventLister("go", historyChange);
// 4.执行设置页面操作
function historyChange() {
  switch(location.pathname) {
     switch(location.pathname) {
       case "/home":
         contentEl.innerHTML = "Home";
         break;
       case "/about":
         contentEl.innerHTML = "About";
         break;
       default: 
         contentEl.innerHTML = "Default";
     }
  }
}
```

## vue-router：

### 认识vue-router：

vue-router是Vue.js的官方路由，它与 Vue.js 核心深度集成，让用 Vue.js 构建单页面应用变得非常容易；

vue-router是基于路由和组件的：

- 路由用于设定访问路径，将路径和组件映射起来；
- 在vue-router的单页面应用中，页面的路径的改变就是组件的切换；

### 安装：

```bash
npm install vue-router@4
```

### 使用：

- 第一步：创建路由组件的组件并导入；

  ```js
  import Home from './pages/Home.vue';
  ```

- 第二步：配置路由映射：组件和路径映射关系的routes数组；

  ```js
  const routes = [
    { path: '/home', components: Home }
  ]
  ```

- 第三步：创建路由对象：通过createRouter创建，并传入routes和history模式；

  ```js
  const router = createRouter({
    routes,
    history: createWebHastory()
  })
  ```

- 第四步：使用路由：通过\<router-link>和\<router-view>;

  ```js
  // main.js
  import router from './router';
  createApp(App).use(router).mount('#app');
  ```

  ```html
  <div class="app">
    <p>
      <router-link to="/home"></router-link>
      <router-link to="/about"></router-link>
    </p>
    <router-view></router-view>
  </div>
  ```

### 路由的默认路径：

例如：我们进入网站时，希望路径默认跳到首页；

在routes中配置映射：

- path：配置的是根路径 /；
- redirect：重定向，即将根路径重定向到/home的路径下；

### router-link :

#### router-link 属性：

- to：是一个字符串或对象；
- replace：设置replace属性的话，当点击时，会调用 router.replace()，而不是router.push()；
- active-class：设置激活a元素后应用的class，默认是router-link-active；
- exact-active-class：链接精准激活后，应用于渲染的\<a>的class，默认是router-link-exact-active；

#### router-link的v-slot：

在vue-router3.x的时候，router-link有一个tag属性，可以决定router-link到底渲染成什么元素：

- 但在vue-router4.x开始，该属性被移除了；
- 而是给我们提供了更具有灵活性的v-slot的方式来定制渲染的内容；

**v-slot的使用：**

- 首先，使用custom表示我们整个元素要自定义

  - 如果不写，那么自定义的内容会被包裹在一个a元素中

- 其次，使用v-slot作用域插槽来获取内部传给我们的值：

  - href：解析后的URL;
  - route：解析后的规范化的route对象；
  - navigate：触发导航的函数；
  - isActive：是否匹配的状态；
  - isExactActive：是否是精准匹配的状态；

  ```html
  <route-link to="/about" v-slot="{ href, route, navigate, isActive, isExactActive}">
    <p @click="navigate">跳转about</p>
    <div>
        <p>href: {{ href }}</p>
        <p>route: {{ route }}</p>
        <p>isActive: {{ isActive }}</p>
        <p>isExactActive: {{ isExactActive }}</p>
    </div>
  </route-link>
  ```

### router-view的v-slot：

router-view也提供给我们一个插槽，可以用于  和  组件来包裹路由组件：

- Component：要渲染的组件； 
- route：解析出的标准化路由对象；

```html
<router-view v-slot="{ Component }">
  <transition name="why">
    <keep-alive>
      <component :is="Component"></component>
    </keep-alive>
  </transition>
</router-view>
```

```css
.router-link-active {
  color: red;
}
.why-enter-from,
.why-leave-to {
  opacity: 0;
}
.why-enter-active,
.why-leave-active {
 transition: opacity 1s ease-in;
}
```

### 路由懒加载：

当打包构建应用时，JavaScript 包会变得非常大，影响页面加载：

- 如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件；
- 这样会更加高效，也可以提高首屏的渲染效率；

同webpack的分包概念，vue-router默认就支持动态来导入组件：

- 因为component可以传入一个组件，也可以接收一个函数，该函数需要放回一个Promise；

- 而import函数就是返回一个Promise；

```js
const route = [
  { path: '/', redirect: '/home'},
  { path: '/home', component: () => import('../pages/Home.vue')}
]
```

### 动态路由：

#### 动态路由基本匹配：

很多时候我们需要将给定匹配模式的路由映射到同一组件：

- 例如，一个User组件，它应该对所有用户进行渲染，但是用户的ID是不同的；
- 在vue-router中，我们在路径中使用一个动态字段来实现，我们称之为路径参数；

```js
{
  path: '/user/:id',
  component: () => import('../pages/User.vue')
}
```

在router-link中进行跳转：

```js
<router-link to="/user/123">用户123</router-link>
```

#### 获取动态路由的值：

那么我们如何获取到上述路由中的值呢？

- 在template中，直接通过 $route.params 获取值；

  ```html
  <h2>{{$route.params.username}}</h2>
  ```

- 在created中，通过 this.$route.params 获取值；

  ```js
  created() {
    console.log(this.$route.params.username);
  }
  ```

- 在setup中，使用vue-router库提供的一个hook：useRoute

  ```js
  import { useRoute } from "vue-router";
  //....
  export default {
    setup() {
      const route = useRoute();
      cosnole.log(route.params.id)
    }
  }
  ```

#### 匹配多个参数：

| 匹配模式              | 匹配路径           | $route.params             |
| --------------------- | ------------------ | ------------------------- |
| /users/:id            | /users/123         | { id: '123' }             |
| /users/:id/info/:name | /users/123/info/zs | { id: '123', name: 'zs' } |

#### NotFound：

对于那些未匹配到的路由，我们通常会匹配到固定的某个页面：

- 例如：NotFound的错误页面中，这时我们可以编写一个动态路由用于匹配所有的页面；

  ```js
  {
    path: '/:pathMatch(.*)',
    component: () =>import('../pages/NotFound.vue')
  }
  ```

- 我们可以通过 $route.params.pathMatch获取传入的参数；

  ```
  <h2>{{$route.params.pathMatch}}</h2>
  ```

**匹配规则加 ***：

这里还有另外一种写法：在 `/:pathMatch(.*)` 后加  `*` ；

```js
{
  path: '/:pathMatch(.*)*',
  component: () =>import('../pages/NotFound.vue')
}
```

它们的区别在于：解析的时候是否解析 `/`

- `path：'/:pathMatch(.*)*'`  -> `["user", "hahaha", "123"]`
- `path：'/:pathMatch(.*)*'`  ->  `user/hahaha/123`

#### 动态添加路由：

某些情况下我们可能需要动态的来添加路由：

- 例如，根据用户不同的权限，注册不同的路由；
- 这时可以使用 addRoute 方法；

```js
// 动态添加一个路由
const categoryRoute = {
  path: '/category',
  component: () => import('../pages/Category.vue')
}
router.addRoute(categoryRoute)
```

- 如果为route添加的是一个children路由，可以传入对应的name；

```js
const homeMomentRoute = {
  path: 'moment',
  component: () => import('../pages/HomeMoment.vue')
}
router.addRoute('home', homeMomentRoute)
```

#### 动态删除路由：

```js
// 先添加一个路由，用于测试
router.addRoute({ path: '/about', name: 'about', component: About });
```

删除路由的方式：

- 方式一：添加一个name相同的路由；

  ```js
  // 这将会删除之前添加的路由
  router.addRoute({ path: '/other', name: 'about', component: 'Home' });
  ```

- 方式二：通过 removeRoute 方法，传入路由的名称；

  ```js
  // 删除路由
  router.removeRoute('about');
  ```

- 方式三：通过 addRoute 方法的返回值回调；

  ```js
  const removeRoute = router.addRoute(routeRecord)
  // 如果路由存在的话就删除
  removeRoute()
  ```

### 路由的其他补充：

- router.hasRoute()：检查路由是否存在；
- router.getRoutes()：获取一个包含所有路由记录的数组；

### 路由导航守卫：

vue-router提供的导航守卫主要用来通过跳转或取消的方式守卫导航。

#### beforeEach：

全局的前置守卫beforeEach是在导航触发时会被回调的；

##### 参数：

- to：即将进入的路由Route对象；
- from：即将离开的路由Route对象；
- next：可选参数
  - vue2中，是通过next函数来决定如何进行跳转的；
  - 但vue3中，是通过返回值来控制的，不再推荐next函数，因为开发中很容易调用多次next；

##### 返回值：

- false：取消当前导航；
- 不返回或者underfined：进行默认导航；
- 返回一个路由地址：
  - 可以是一个string类型的路径；
  - 可以是一个对象，对象中包含 path、query、params 等信息；

##### 案例：登录导航守卫

- 功能：只有登录后才能看到其他页面；

```js
// router/index.js
router.beforeEach((to, from) => {
  if (to.path !== '/login') {
    const token = window.localStorage.getItem('token');
    if (!token) {
      return {
        path: '/login'
      }
    }
  }
})
```

```js
setup() {
  const router = useRouter();
  const login = () => {
    window.localStorage.setItem('token', '123');
    router.push({
      path: '/home'
    })
  }
  return { login }
}
```

##### 其他导航守卫：

 Vue还提供了很多的其他守卫函数，目的都是在某一个时刻给予我们回调，让我们可以更好的控制程序的流程或者功能：

-  https://next.router.vuejs.org/zh/guide/advanced/navigation-guards.html 

##### 完整的导航解析流程：

- 导航被触发；
- 在失活的组件里调用 beforeRouteLeave 守卫；
- 调用全局的 beforeEach 守卫；
- 在重用的组件里调用 beforeRouteUpdate 守卫（2.2+）；
- 在路由配置里调用 beforeEnter；
- 解析异步路由组件；
- 在被激活的组件里调用 beforeRouteEnter；
- 调用全局的 beforeResolve 守卫（2.5+）；
- 导航被确认；
- 调用全局的 afferEach 钩子；
- 触发DOM更新；
- 调用 beforeRouteEnter 守卫中传给next 的回调函数，创建好的组件实例会作为回调函数的参数传入；

### 路由的嵌套：

我们可以在一些底层路由，如：Home、User等之间进行来回切换；

但是，Home页面本身，也可能会在多个组件之间来回切换：

- 例如，Home中可以包括Product、Message，它们可以在Home内部来回切换；

这个时候就需要使用嵌套路由，在Home中页使用router-view来占位之后需要渲染的组件；

```js
{ 
  path: '/home', 
  component: () => import('../pages/Home.vue')，
  children： [
    {
      path: '',
      redirect: '/home/product'
    },
    {
      path: 'product',
      component: () => import('../pages/HomeProduct.vue')
    }
    // ...
  ]
}
```

### 代码的页面跳转：

有时候我们希望通过代码来完成页面的跳转，比如通过点击一个按钮：

```js
onBtn() {
  // this.$router.push('/profile')
  this.$router.push({
    path: '/profile'
  })
}
```

如果是在setup中编写的代码，那么我们可以通过 useRouter 来获取：

```js
const router = useRouter()
const onBtn = () => {
  router,replace('./profile')
}
```

**query方式的参数：**

- 我们也可以通过query的方式来传递参数：

  ```js
  this.$router.push({
    path: '/profile'，
    query: { name:'zs' }
  })
  ```

- 在页面中通过$route.query来获取参数：

  ```html
  <h2>{{$route.query.name}}</h2>
  ```

**替换当前的位置：**

- 使用push的特点是压入一个新的页面，那么在用户点击返回时，还可以回退到上一个页面；

- 如果我们希望当前页面是一个替换操作，可以使用replace：

| `声明式`                         | `编程式`              |
| -------------------------------- | --------------------- |
| `<route-link :to="..." replace>` | `router.replace(...)` |

**页面的前进后退：**

- router的 go() 方法：

  ```js
  router.go(1);	// 前进一条记录
  router.go(-1);	// 后退一条记录
  
  router.go(100);	// 如果没有那么多记录，静默失败
  ```

- router也有back：

  - 通过调用 history.back() 回溯历史。相当于 router.go(-1)； 

- router也有forward： 

  - 通过调用 history.forward() 在历史中前进。相当于 router.go(1)；







