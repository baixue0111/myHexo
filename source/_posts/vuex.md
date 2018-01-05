---
title: Vuex 学习详解
date: 2017-11-12
tags: [vue, vuex]
categories: 
        - vue
        - vuex
---
**vuex**是一个专门为vue.js设计的集中式管理状态管理架构。状态？我把它理解为在data中属性需要共享给其他vue组件使用的部分，就叫做状态。简单的说就是**data中需要共用的属性**。比如：我们有几个页面需要显示用户名和用户等级，或者是显示用户的地理位置。如果我们不把这些属性设置为状态，那没个页面遇到后，都会到服务器进行查找计算，返回后在显示。在中大型项目中回有很多公用的数据。
### 先来一个小Demo
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
这里的mutations是故宫的写法，意思是改变的，如果我们要改变state的数值，我们就需要把改变其的方法必须写在mutations里。
5、在count.vue模板中加入两个按钮，并调用mutations中的方法。
``` html
<div>
    <button @click="$store.commit('add')"> + </button>
    <button @click="$store.commit('reduce')"> - </button>
</div>
```
这样进行预览就可以实现对vuex中的count进行加减了。
### state访问状态对象
在第一节我们已经写了一个const state，这个就是我们说的访问状态对象，它就是我们SPA(单页应用程序)中的共享值，接下来我们主要学习状态对象赋值给内部对象，也就是把store.js中的值，赋值给我们模板里data中的值。有三种赋值方式：
#### 一、通过computed的计算属性直接赋值
computed属性可以在输出前，对data中的值进行改变，我们就利用这种特性把store.js中的state值赋值给模板中的data的值。
``` JavaScript
computed: {
    count() {
        return this.$store.state.count
    }
}
```
这里需要注意的是return $store.state.count这一句，一定要写this，要不然你会找不到$store的。这种写法很好理解，但是写起来比较麻烦。
#### 二、通过mapState的对象来赋值
首先先用import引入mapState。
``` JavaScript
import {mapState} from 'vuex';
```
然后在computed计算属性里写下如下代码：
``` JavaScript
computed: mapState({
    count: state => state.count
})
```
这里使用了ES6的箭头函数来count赋值。
#### 三、通过mapState的数组来赋值
``` JavaScript
computed: mapState(["count"])
```
这个算是简单的写法了，在实际项目开发当中也经常这样使用
### Mutations修改状态
#### $store.commit()
Vuex提供了commit方法来修改状态，我们在前面例子的基础上进行修改。
``` html
<button @click="$store.commit('add')"> + </button>
<button @click="$store.commit('reduce')"> - </button>
```
store.js文件：
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
这只是一个最简单的修改状态的操作，在实际项目开发中我们常常需要在修改状态时传值，上面这个例子时每次只加1，而现在我们要通过所传的值进行相加。其实我们只需要在Mutations里面加上一个参数，并在commit的时候传递就可以了。代码如下：
``` JavaScript
const mutations = {
    add(state, n) {
        state.count += n;
    },
    reduce(state) {
        state.count --;
    }
}
```
在count.vue里修改按钮的commit()方法传递的参数，我们传递10，意思就是每次加10.
``` html
<p>
    <button @click="$store.commit('add', 10)"> + </button>
    <button @click="$store.commit('reduce')"> - </button>
</p>
```
#### 模板获取Mutations方法：
实际开发中我们也不喜欢看到$store.commit()这样的方法出现，我们希望跟调用模板里的方法一样调用。
例如： @click="reduce"就和没引用vuex模板一样。要达到这种写法，只需要简单的两步就可以了：
* 在模板count.vue里面用import引入我们的mapMutations:
``` JavaScript
import { mapState, mapMutations} from 'vuex';
```
* 在模板的**script**标签里添加**methods**属性，并加入mapMutations
``` JavaScript
methods: mapMutations({
    'add', 'reduce'
})
```
通过以上两部，我们已经可以在模板中直接使用我们的reduce或者add方法了，就像下面这样：
``` html
<button @click="reduce">-</button>
```
### getters计算过滤操作
getters从表面理解是获得的意思，可以把它看作在获取数据之前进行的一种再编辑，相当于对数据的一个过滤和加工。可以把它看作是store.js的计算属性。

#### getters基本用法：
比如我们现在要对store.js文件中的count进行一个计算属性的操作，就是在它输出前，给它加上100。首先要在store.js里用const声明我们的getters属性。
``` JavaScript
cosnt getters = {
    count: function(state) {
        return state.count += 100;
    }
}
```
写好getters之后，然后需要在vuex.Store()里引入，由于之前我们已经引入了state和mutations，所以引入里有三个引入属性。
``` JavaScript
export default Vuex.Store({
    state, mutations, getters
})
```
在store.js里的配置算是完成了，我们需要到模板页对computed进行配置。在vue的构造器里边只能有一个computed属性，如果你写多个，只有最后一个computed属性可用，所以要对之前的computed属性进行修改。使用ES6中的展开运算符“...”。
``` JavaScript

comouted: {
    ...mapState(["count"]),
    count() {
        return this.$store.getters.count;
    }
}
```
需要注意的是，写了这个配置后，在每次count的值发生变化的时候，都会进行加100的操作。
#### 用mapGetters简化模板的写法：
上面我们把state和mutations都用map的引用方法把模板中的代码进行简化，那么getters也可以，首先用import引入mapGetters。
``` JavaScript
import { mapState,mapMutations, mapGetters } from 'vuex'
```
在computed属性中加入mapGetters:
``` JavaScript
...mapGetters(['count'])。
```
### actions异步修改状态
actions和之前的Mutations功能基本是一样的，不同点是：actions是异步的改变state状态，而Mutations是同步改变状态。
#### 在store.js中声明actions
actions是可以调用Mutations里的方法的，接着上面的例子接着修改，在actions里调用add和reduce两个方法：
``` JavaScript
const actions = {
    addAction(context) {
        context.commit('add', 100);
    },
    reduceAction({commit}) {
        commit('reduce');
    }
}
```
在actions里面写了两个方法addAction和reduceAction，在方法里，都用commit调用了Mutations里的方法，但是两个方法传递的参数却不一样。
* context: 上下文对象，也可以理解成store本身
* {commit}: 直接把commit对象传递进来，可以让方法体逻辑和代码更清晰了。
#### 在模板中如何使用：
需要在count.vue模板中编写代码，让actions生效。先复制之前的两个按钮，并把其方法分别改为：addAction和reduceAction。
``` html
<p>
    <button @click="addAction"> + </button>
    <button @click="reduceAction"> - </button>
</p>
```
然后修改methods方法，首先用import把mapActions引入
``` JavaScript
import {mapState, mapMutations, mapActions} from 'vuex';
```
然后用扩展运算符把mapMutations和mapActions加入。
``` JavaScript
methods: {
    ...mapMutations([
        'add', 'reduce'
    ]),
    ...mapActions(['addAction', 'reduceAction'])
}
```
#### 增加异步校验
为了测试一下actions的异步功能，增加一个计时器(setTimeout)延迟执行，在addAction里使用setTimeout进行延迟执行。
``` JavaScript
setTimeout(() => {context.commit('reduce')}, 3000);
console.log("我比reduce提前输出");
```
### 状态管理器的module模板组
当一个项目越来越复杂，那么所要共享的状态也越来越多，这时就需要把状态的各种操作进行一个分组，分组后再进行按组编写。
#### 声明模块组：
在vuex/store.js中声明模块组，还是用const常量的方法声明模块组。代码如下：
``` JavaScript
const moduleA = {
    state, mutations, getters, actions
}
```
声明好之后，需要修改原来Vuex.store里的值：
``` JavaScript
export default new Vuex.store({
    modules: {a: moduleA}
})
```
#### 在模板中使用：
在模板中使用插值的形式使用count状态：
``` html
<h3>{{$store.state.a.count}}</h3>
```
如果想用简单的方法引入，还是要在计算属性中return返回状态：
``` JavaScript
computed: {
    count() {
        return this.$store.state.a.count;
    }
}
```
原文链接：(http://jspang.com/2017/05/03/vuex/)