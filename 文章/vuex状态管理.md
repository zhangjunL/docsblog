## vuex 简介
在Vue.js应用程序中，Vuex是一个状态管理库，用于管理应用程序的状态。它特别适用于大型复杂的单页面应用程序，其中组件之间共享状态的管理变得复杂。以下是一些基本的Vuex概念和用法：

State（状态）: 代表应用程序的状态，存储在单一的状态树中。

Getters（获取器）: 用于从状态中派生出一些新的状态，类似于计算属性。

Mutations（变更）: 是唯一能够修改状态的地方，确保状态的可追踪性。

Actions（动作）: 类似于Mutations，但是它是异步的，可以包含业务逻辑、异步操作，然后提交Mutation。

Modules（模块）: 允许将Store拆分为多个模块，每个模块具有自己的state、getters、mutations和actions。

安装vuex 
```bash
npm install vuex --save
```
创建store.js文件

```js
// store.js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    count: 0,
  },
  mutations: {
    increment(state) {
      state.count++;
    },
  },
  actions: {
    asyncIncrement(context) {
      setTimeout(() => {
        context.commit('increment');
      }, 1000);
    },
  },
  getters: {
    doubleCount(state) {
      return state.count * 2;
    },
  },
});

export default store;
```
在main.js中引入store.js
```js
// main.js
import Vue from 'vue';
import App from './App.vue';
import store from './store';
new Vue({
  store,
  render: h => h(App),
}).$mount('#app');

```
在组件中使用store
```js
// YourComponent.vue
<template>
  <div>
    <p>Count: {{ $store.state.count }}</p>
    <p>Double Count: {{ $store.getters.doubleCount }}</p>
    <button @click="increment">Increment</button>
    <button @click="asyncIncrement">Async Increment</button>
  </div>
</template>

<script>
export default {
  methods: {
    increment() {
      this.$store.commit('increment');
    },
    asyncIncrement() {
      this.$store.dispatch('asyncIncrement');
    },
  },
};
</script>
// YourComponent.vue
<template>
  <div>
    <p>Count: {{ $store.state.count }}</p>
    <p>Double Count: {{ $store.getters.doubleCount }}</p>
    <button @click="increment">Increment</button>
    <button @click="asyncIncrement">Async Increment</button>
  </div>
</template>

<script>
export default {
  methods: {
    increment() {
      this.$store.commit('increment');
    },
    asyncIncrement() {
      this.$store.dispatch('asyncIncrement'); //dispatch action to store
    },
  },
};
</script>

```

```js
//使用状态
this.$store.state.count
//使用getter
this.$store.getters.doubleCount
//使用mutation
this.$store.commit('increment')
//使用action
this.$store.dispatch('asyncIncrement')

```

