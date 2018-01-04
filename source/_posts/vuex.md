---
title: Vuex 详解
date: 2017-11-12
tags: [vue, vuex]
categories: 
        - vue
        - vuex
---
**vuex**是一个专门为vue.js设计的集中式管理状态管理架构。状态？我把它理解为在data中属性需要共享给其他vue组件使用的部分，就叫做状态。简单的说就是**data中需要共用的属性**。比如：我们有几个页面需要显示用户名和用户等级，或者是显示用户的地理位置。如果我们不把这些属性设置为状态，那没个页面遇到后，都会到服务器进行查找计算，返回后在显示。在中大型项目中回有很多公用的数据。
### 小Demo
#### 引入Vuex
1、利用npm包管理工具，进行安装vuex。在控制命令行中输入下边的命令就可以了。
``` JavaScript
npm install vuex --save
```
需要注意的是这里一定要加上 --save，因为你这个包我们在生产环境中是要使用的。
2、新建一个vuex文件夹（这个也不是必须的）,并在文件夹下新建store.js文件，文件中引入我们的vue和vuex。
``` JavaScript
import Vue from 'vue';
import Vuex from 'vuex';
```
<!--more-->
3、使用vuex，引入之后用Vue.use进行引用。
``` JavaScript
Vue.use(Vuex);
```
通过这三步的操作，vuex就算引用成功了，接下来我们就可以使用了。
#### 入门案例
我们这个小案例先声明一个state的count状态，在页面中使用显示这个count，然后可以利用按钮就行加减。
1、现在我们往store.js文件里增加一个常量对象。
``` JavaScript
const state = {
    count: 1
}
```
2、用export default封装代码，让外部可以引用。
``` JavaScript
export default new Vuex.store({
    state
})
```
3、新建一个vue模板，位置在components文件夹下，名字叫count.vue。在模板中我们引入我们刚新建的store.js文件，并在模板中用{{$store.state.count}}输出count的值。
``` html
<template>
    <div>
        <h2>{{msg}}</h2>
        <hr>
        <h3>{{$store.state.count}}</h3>
    </div>
</template>
<script>
    import store from '@/vuex/store';
    export default {
        data() {
            return {
                msg: 'Hello Vuex'
            }
        },
        store
    }
</script>
```
4、在store.js文件中加入两个改变state的方法。
``` JavaScript
const mutations = {
    add(state) {
        state.count ++;
    },
    reduce(state) {
        state.count --;
    }
}
```
这里