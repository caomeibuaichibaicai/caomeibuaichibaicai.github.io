---
title: vuex笔记
date: 2018-04-16 18:41:15
tags:
  - vuex
categories: Vue
---
### 1.通过在根组件，设置store选项，将状态从根组件注入到下面所有的子组件中
类似：
```javascript
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
```
需要调用Vue.use(Vuex)，这样就不需要在每个组件中频繁导入store了，子组件通过this.$store就可以访问到
### 2.mapState 辅助函数帮助我们生成计算属性,从而从store实例中读取多个state状态,vuex的状态存储是响应式的,写在computed中
### 3.getters可以理解为store的计算属性，可以通过两种方式来访问：
（1）通过属性访问，属性的getter函数可以接受两个参数，第一个表示计算属性依赖的state状态（默认要有），第二个表示计算属性依赖的其他getter属性。访问getters使用属性访问时，用store.getters.属性，同样它也是依赖缓存进行计算的，如下：
```javascript
getters: {
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```
（2）通过方法访问，让getter返回一个带形参的箭头函数，并且给getter传递实参来实现访问getter的值。这是个函数柯里化的形式，第一个参数默认为state获取全局状态，第二个参数是返回的函数自带的自定义参数，后面还可以接其他自定义参数。getter在通过方法访问时，只要调用就会访问，不缓存结果，调用时store.getters.方法名（实参），写法如下：
```javascript
getters: {
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
```
### 4.mapGetters将store中的多个getter映射到局部计算属性中，类似mapState，也要写在computed中
### 5.mutation是vuex中唯一改变store中状态的方法
commit提交一个mutation就可以，在mutations中写一个修改状态的回调函数，这个回调函数可以包含两个参数，第一个state是获取store中的状态（必须）；第二个是传入的要修改成的值，称之为payload荷载，大多数情况下荷载为一个对象。每个mutation都包含一个字符串的事件类型即type和回调函数即handler,其中type就是mutation的事件名，也称之为事件类型。最好使用常量代理mutation的事件名。


另外，在调用时，不能直接调用mutation handler，可以看成mutation是单单在注册，所以在调用修改状态的方法要使用store.commit()调用，例如：
```javascript
//注册：
mutations:{
	setDataNumber (state,data) {
		state.dataNumber = data.num
 	}
}
//调用一：
store.commit(‘setDataNumber’, { num: 4} )
//commit里面的{num:4}对象作为荷载存在
//
//
//调用二：
store.commit({type:’setDataNumber’,num:4})
//commit里面的整个对象作为荷载存在
```
**注意**：mutation必须是同步函数，因为如果在mutation中写入异步函数，其中的回调函数是无法追踪到什么时候被调用的，那么其中的状态改变也是不能被追踪到的。
### 6.mapMutations辅助函数可以将methods映射为store.commit调用
在辅助函数内写入mutation的事件类型，mapMutations写在methods中调用,如：
```javascript
methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
 // `mapMutations` 也支持载荷：
      'incrementBy' //将`this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ])
  }
```
### 7.action是异步的mutation
它可以改变状态，但是在action内部提交的是mutation。而且重要的一点是，action内部可以包含异步操作，是异步的，支持多重分发mutation,多个mutation同时异步调用。

action函数可以接受一个和store具有相同方法和属性的context对象，可以使用context对象来commit提交mutation，也可以通过context来获取state和getters以及dispatch都可以，例如：
```javascript
actions: {
    increment (context) {
      context.commit('increment')
    }
  }
//或者不写context
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```
action的分发调用：使用store.dispatch(‘action名’)调用，它可以处理action内部异步返回的promise，并且dispatch返回的仍是一个promise对象，所以后面可以接then()或者catch()这些方法。另外同mutation一样，添加荷载的话，有两种调用方式。
```javascript
// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```
### 8.mapActions辅助函数
它写在methods中，将组件的methods映射为$store.dispatch调用，详细例子如下：
```javascript
import { mapActions } from 'vuex'

export default {
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```
### 9.action的异步用法
例如：
```javascript
store.dispatch('actionA').then(() => {
  // ...
})
//或者
actions: {
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
//或者使用async以及await来表示异步
actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```
这样，一个dispatch可以在不同模块中触发多个action
### 10.module模块
用来解决单一状态树的状态过多造成的臃肿问题，将store分成多个module,每个module都有自己的state,getter,mutation以及action，集成store的方法如下：
```javascript
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}
const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})
```
**(1)访问不同模块上的状态state**
*****这里注意！**state是唯一**会根据组合时模块的别名来添加层级的，后面的 getters、mutations 以及 actions 都是直接合并在 store 下，所以比如获取moduleA中的state里面的count状态值：store.state.a.count
**(2)访问不同模块上的getter**
这个就与state不同了，不同模块的 getters 会直接合并在 store.getters 下，所以比如获取moduleA中的getter里面的doneTodosCount属性：store.getter.doneTodosCount
*****这里要注意，因为getters不区别模块，所以不可重名，否则会报错。
**(3)getters模块化后新增的第三个参数rootState**
这个参数是用来获取全局的state,这样不同的模块就能访问其他所有的模块状态state。
**(4）访问不同模块上的mutation**
同getter类似，不同模块的 mutation 均可以通过 store.commit 直接触发。
