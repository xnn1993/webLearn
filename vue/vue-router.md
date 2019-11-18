## 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用离开守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 用创建好的实例调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数。



## 在vue-cli3中使用vue-router

`vue add router`

- 生成的路由配置

```js
//router.index

import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '../views/Home.vue'

//在Vue中引入VueRouter插件
Vue.use(VueRouter)

const routes = [
  {
    path: '/', //地址
    name: 'home', //命名路由
    component: Home //地址对应的组件
  },
  {
    path: '/about',
    name: 'about',
    // 路由层级代码分割，生成分片(about.[hash].js)
    // 当路由访问时会懒加载
    component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
  }
]

const router = new VueRouter({
  mode: 'history', //模式： hash/history/abstract
  base: process.env.BASE_URL, //基地址，不会列入路由的匹配范围
  routes
})

export default router

```

- 指定路由器

```js
//main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'

new Vue({
  router,
  render: h => h(App)
}).$mount('#app')
```

- 路由视图

```html
<router-view/>
```

- 导航链接

```html
<router-link to="/">Home</router-link>
<router-link to="/about">About</router-link>
```



## 路由守卫

- 全局前置守卫`beforeEach`

  ```js
  const router = new VueRouter({
    routes
  })
  
  //beforeEach 所有路由均通过
  router.beforeEach((to, from, next) => {
      if (to.meta.auth && !window.isLogin) {
          if (window.confirm('请登录')) {
              window.isLogin = true;
              next();
          } else {
              next('/');
          }
      } else {
          next()
      }
  })
  ```

- 路由独享守卫`beforeEnter`

  ```js
  //beforeEnter 某个路由自己设置的
  beforeEnter(to, from, next){
      if (!window.isLogin) {
          alert('未登录');
          next('/');
      } else {
          next();
      }
  }
  ```

- 组件内的守卫

  ```js
  beforeRouteEnter(to, form, next){};
  beforeRouteUpdate(to, form, next){};
  beforeRouteLeave(to, form, next){};
  ```

  



## 底层实现

- vue-router本质是一个插件,实现插件

  实现一个vue插件的方式：实现该类的install方法即可

  ```js
  class VueRouter{
  	init(){}
  }
  VueRouter.install = function(_Vue){
      //混入
      _Vue.mixin({
          beforeCreate(){
              if(this.$options.router) {
                  //将vue配置中router挂载到$router中，是其实例可以通过$router获取路由配置
                  //仅在根组件执行一次，挂在vue上
                  _Vue.prototype.$router = this.$options.router;
                  this.$options.route.init();
              }
          }
      })
  }
  
  //使用插件
  Vue.use(VueRouter);
  ```

  

- url变化的监听

  ```js
  bindEvents(){
      //当一个资源及其依赖资源已完成加载时，将触发load事件。
      window.addEventListener('load', this.onHashChange);
      //当URL的片段标识符更改时，将触发hashchange事件 (跟在＃符号后面的URL部分，包括＃符号)
      window.addEventListener('hashchange', this.onHashChange.bind(this))
  }
  onHashChange(){
      //window.location.hash 包含块标识符的DOMString，开头有一个“#”。
      this.app.current = window.location.hash.slice(1) || '/'
  }
  ```

  

- 路由配置的解析，解析为：{‘url’:  组件}

  ```js
  createRouteMap(){
      this.$options.routes.forEach(item => {
          this.routeMap[item.path] = item.component;
      })
  }
  ```

- 实现全局组件：router-link   router-view

  

  