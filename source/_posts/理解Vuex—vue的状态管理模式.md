---
title: 理解vuex--vue的状态管理模式
date: 2017-11-12
tags: [vue, vuex]
categories:
        - vue
        - vuex
---
### vuex是什么？
**vuex是一个专为Vue.js应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。**
状态管理模式、集中式存储管理一听就很高大，蛮吓人的。在我看来vuex就是把需要共享的变量全部存储在一个对象里面，然后将这个对象放在顶层组件中供其他组件使用。这么说吧，将vue想作是一个js文件、组件是函数，那么vuex就是一个全局变量，只是这个“全局变量”包含了一些特定的规则而已。
在vue的组件化开发中，经常会遇到需要将当前组件的状态传递给其他组件。父子组件通信时，我们通常采用prop + emit这种方式。但当通信双方不是父子组件甚至压根不存在相关联系，或者一个字状态需要共享给多个组件时，就会非常麻烦，数据也会相当难维护，这对我们开发来讲就很不友好。vuex这个时候就很实用，不过在使用vuex之后也带来了更多的概念和框架，需慎重！
### vuex里面都有些什么内容？
``` JavaScript
const store = new Vuex.Store({
    state: {
        name: 'weish',
        age: 22
    },
    getters: {
        personInfo(state) {
            return `My name is ${state.name}, I am ${state.age}`;
        }
    },
    mutations: {
        SET_AGE(state, age) {
            commit(age, age);
        }
    },
    actions: {
        nameAsyn({commit}) {
            setTimeout(() => {
                commit('SET_AGE', 18);
            }, 1000);
        }
    },
    modules: {
        a: modulesA
    }
})
```
<!--more-->
这个就是最基本也是完整的vuex代码；vuex包含5个基本的对象：
* state: 存储状态。也就是变量；
* getters: 派生状态。也就是set、get中的get，有两个可选参数：state、getters分别可以获取state中的变量和其他的getters。外部调用方式：store.getters.personInfo()。就和vue的computed差不多；
* mutations: 提交状态修改。也就是set、get中的set，这是唯一修改state的方式，但不支持异步操作。第一个参数默认是state。外部调用方式：store.commit('SET_AGE', 18)。和vue中的methods类似。
* actions: 和mutations类似。不过actions支持异步操作。第一个参数默认是和store具有相同参数属性的对象。外部调用方式：store.dispatch('nameAsyn')。
* modules: store的子模块，内容就相当于是store的一个实例。调用方式和前面介绍的相似，只是要加上当前子模块名，如：store.a.getters.xxx()。
### vue-cli中使用vuex的方式
一般来讲，我们都会采用vue-cli来进行实际的开发，在vue-cli中，开发和调用方式稍微不同。
``` JavaScript
|——index.html
|——main.js
|——components
|__store
    |——index.js     // 我们组装模块并导出store的地方
    |——state.js     // 根级别的state
    |——getters.js   // 根级别的getter
    |——mutation-types.js   // 根级别的mutations名称（官方推荐mutations方法名使用大写）
    |——mutations.js   // 根级别的mutation
    |——actions.js     // 根级别的action
    |__modules
        |——m1.js      // 模块1
        |——m2.js      // 模块2
```
下面是state.js示例：
``` JavaScript
const state = {
    name: 'weish',
    age: 22
};
export default state;
```
下面是getters.js示例（我们一般使用getters来获取state的状态，而不是直接使用state):
``` JavaScript
export const name = (state) => {
    return state.name;
}
export const age = (state) => {
    return state.age;
}
export const other = (state) => {
    return `My name is ${state.name}, I am ${state.age}.`;
}
```
mutation-type.js示例（我们会将所有mutations的函数名放在这个文件里）：
``` JavaScript
export const SET_NAME = 'SET_NAME';
export const SET_AGE = 'SET_AGE';
```
下面是mutations.js示例：
``` JavaScript
import * as types from './mutation-type.js';
export default {
    [types.SET_NAME](state, name) {
        state.name = name;
    },
    [types.SET_AGE](state, age) {
        state.age = age;
    }
};
```
下面是actions.js示例（异步操作多个commit时）：
``` JavaScript
import * as types from './mutation-type.js';
export default {
    nameAsyn({commit}, {age, name}) {
        commit(types.SET_NAME, name);
        commit(types.SET_AGE, age);
    }
};
```
下面是modules-m1.js示例（如果不是很复杂的应用，一般来讲是不会分模块的）：
``` JavaScript
export default {
    state: {},
    getters: {},
    mutations: {},
    actions: {}
};
```
下面是index.js示例（组装vuex）：
``` JavaScript
import vue from 'vue';
import vuex from 'vuex';
import state from './state.js';
import * as getters from './getters.js';
import mutations from './mutations.js';
import actions from './actions.js';
import m1 from './modules/m1.js';
import m2 from './modules/m2.js';
import createLogger from 'vuex/dist/logger';  // 修改日志

vue.use(vuex);

const debug = process.env.NODE_ENV !== 'production';   // 开发环境中为true, 否则为false

export default new vuex.Store({
    state,
    getters,
    mutations,
    actions,
    modules: {
        m1,
        m2
    },
    plugins: debug ? [createLogger()] : []   // 开发环境下显示vuex的修改状态
});
```
最后将store实例挂载到main.js里面的vue上去就行了。
``` JavaScript
import store from './store/index.js';
new Vue({
    el: '#app',
    store,
    render: h => h(App)
});
```
在vue组件中使用时，我们通常会使用mapGetters、mapActions、mapMutations，然后就可以按照vue调用methods和computed的方式去调用这些变量或函数，示例如下：
``` JavaScript
import {mapGetters, mapMutations, mapActions} from 'vuex';

/*只写组件中的script部分*/
export default {
    computed: {
        ...mapGetters([
            name,
            age
        ])
    },
    methods: {
        ...mapMutations({
            setName: 'SET_NAME',
            setAgw: 'SET_AGE'
        }),
        ...mapActions([
            nameAsyn
        ])
    }
};
```
原文链接：[https://segmentfault.com/a/1190000012015742]