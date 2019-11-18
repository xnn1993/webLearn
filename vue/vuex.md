![image-20191101142949780](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191101142949780.png)



## vue lic整合vuex

`vue add vuex`

自动生成store/index.js,并且将store添加入main中new Vue实例时

```js
//store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
  },
  mutations: {
  },
  actions: {
  },
  modules: {
  }
})

//main.js
new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```





## 核心概念
- state 状态、数据
- mutations 更改状态的函数，同步操作
- actions 异步操作，更改状态
- store 包含以上概念的容器



### 获取数值

`this.$store.state.xxx`

```html
<button @click="add">{{$store.state.count}}</button>
```



### 更新数值

`this.$store.commit('mutation名', args)`

```js
add(){
    this.$store.commit('inc', 2);
}
```



### getters - 派生属性

类似于计算属性

```html
//store/index.js
getters: {
    score(state){
        return `累计counte: ${state.count}`
    }
}


//getter变量使用
<p>
    {{$store.getters.score}}
</p>
```



### actions - 异步操作属性

复杂的业务逻辑，适用于异步操作中

```js
//store/index.js
actions: {
    incAsync({commit}){
        setTimeout(()=>{
            commit('inc', 3)
        }, 2000)
    }
}

//actions中数据的使用
<button @click="asyncOpr">{{$store.state.count}}</button>
asyncOpr(){
    this.$store.dispatch('incAsync');
}

```



### 模块化

每个模块封装自己的状态仓库进行管理

```js
//模块counter的vuex的状态封装
export default {
    namespaced: true,
    state: {
        count: 0
    },
    getters: {
        score(state){
            return `累计counte: ${state.count}`
        }
    },
    mutations: {
        inc(state, n=1) {
            state.count += n;
        }
    },
    actions: {
        incAsync({commit}){
            setTimeout(()=>{
                commit('inc', 30)
            }, 2000)
        }
    },
}

//store/index.js
import counter from './test'

Vue.use(Vuex)

export default new Vuex.Store({
  modules: {
    a: counter
  }
})

//模块中vuex的状态数据的使用
<template>
  <div class="home">
      <p>
          {{$store.getters['a/score']}} <!-- 使用getters中的数据 -->
      </p>
      <button @click="addModel">{{$store.state.a.count}}</button>
      <button @click="asyncOprModel">{{$store.state.a.count}}</button>
  </div>
</template>

<script>
export default {
    methods: {
        addModel(){
            this.$store.commit('a/inc', 20); //更新模块中的数据
        },
        asyncOprModel(){
            this.$store.dispatch('a/incAsync'); //更新模块中的数据
        }
    }
}
</script>

```



## 底层实现

- vuex是一个插件
- state/mutations/actions/getters
- 借助vue实现数据的响应式，动态更新页面

```js
//kStore.js
//import Vue from 'vue';
let Vue = null;

class Store {
    constructor(options){
        this.state = new Vue({
            data: options.state
        });

        this.mutations = options.mutations;
        this.actions = options.actions;
        this.getters = {};
        this.handleGetters(options.getters);
    }

    //声明为箭头函数=>dispatch回调中会调用commit，可能会引起this指向的变更
    commit = (type, arg) => {
        this.mutations[type](this.state , arg);
    };
    dispatch(type, arg) {
        this.actions[type]({
            state: this.state,
            commit: this.commit
        }, arg)
    }
    handleGetters(getters){
        if(!getters) {
            return
        }

        //为this.getters定义只读属性
        Object.keys(getters).forEach(key=>{
            Object.defineProperty(this.getters, key, {
                get: ()=>{
                    return getters[key](this.state);
                }
            })
        })
    }
}

function install(_Vue) {
    Vue = _Vue;
    Vue.mixin({
        beforeCreate(){
            if(this.$options.store) {
                _Vue.prototype.$store = this.$options.store;
            }
        }
    })
}

export default {
    Store,
    install
}
```

```js
//使用上文实现的store
import Vue from 'vue'
import App from './App.vue'
import router from './krouter'
import Vuex from './kStore';

Vue.use(Vuex);
let store = new Vuex.Store({
    state: {
        count: 0
    },
    getters: {
        score(state){
            return `累计counte: ${state.count}`
        }
    },
    mutations: {
        inc(state, n=1) {
            state.count += n;
        }
    },
    actions: {
        incAsync({commit}){
            setTimeout(()=>{
                commit('inc', 30)
            }, 2000)
        }
    },
});

new Vue({
    router,
    store,
    render: h => h(App)
}).$mount('#app')

```

