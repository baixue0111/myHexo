---
title: vue.js组件之间传递数据
date: 2017-11-12
tags: [vue]
categories: 
        - vue
---
组件时vue.js最强大的功能之一，而组件实例的作用域时相互独立的，这就意味着不同组件之间的数据无法相互引用。如何传递数据也成了组件的重要知识点之一。
### 组件
组件与组件之间，还存在着不同的关系。父子关系与兄弟关系（不是父子的都暂称为兄弟吧）。
### 父子组件
父子关系即是组件A在它的模板中使用了组件B，那么组件A就是父组件，组件B就是子组件。
``` JavaScript
// 注册一个子组件
Vue.component('child', {
    data: function() {
        return {
            text: '我是father的子组件！'
        }
    },
    template: '<span>{{text}}</span>'
})

// 注册一个父组件
Vue.cpmponent('father', {
    template: '<div><child></child></div>'    // 在模板中使用了child组件
})
```
<!--more-->
直接使用father组件的时候：
``` html
<div id="app">
    <father></father>
</div>
```
页面中就会渲染出：我是father的子组件！
father组件在模板中使用了child组件，所以他说就是父组件，child组件被使用，所以child组件就是子组件。
### 兄弟组件
两个组件互不引用，则为兄弟组件。
``` JavaScript
Vue.component('brother1', {
    template: '<div>我是大哥</div>'
})
Vue.component('beother2', {
    template: '<div>我是小弟</div>'
})
```
使用组件的时候
``` html
<div id="app">
    <brother1></brother1>
    <brother2></brother2>
</div>
```
页面中就会渲染出：
我是大哥
我是小弟
### Prop
子组件想要使用父组件的数据，我们需要通过子组件的props选项来获得父组件传过来的数据。以下我使用在.vue文件中的格式来写例子：
#### 如何传递数据
在父组件father.vue中引用子组件child.vue，把name的值传给child组件。
``` JavaScript
<template>
    <div class="app">
        // message 定义在子组件的props中
        <child :message="name" ></child>
    </div>
</template>
<script>
    import child from './child.vue';
    export default {
        components: {
            child
        },
        data() {
            return {
                name: 'xiaobai'
            }
        }
    }
</script>
```
在子组件child.vue中的props选项中声明它想要获得的数据
``` JavaScript
<template>
    <span>Hello {{message}}</span>
</template>
<script>
    export default {
        // 在props中声明获取父组件的数据通过message传过来
        props: ['message']
    }
</script>
```
那么页面中就会渲染出Hello xiaobai
#### 单项数据流
当父组件的name发生改变，子组件也会自动更新视图。但是在子组件中，我们不要去修改prop。如果你必须要修改到这些数据，你可以使用以下方法：
1、把prop赋值给一个局部变量，然后需要修改的话就修改这个局部变量，而不影响prop
``` JavaScript
export default {
    data() {
        return {
            newMessage: null
        }
    },
    props: ['message'],
    created() {
        this.newMessage = this.message;
    }
}
```
2、在计算属性中对prop进行处理
``` JavaScript
export default {
    props: ['message'],
    computed: {
        newMessage() {
            return this.newMessage + '哈哈哈';
        }
    }
}
```
### 自定义事件
prop是单向绑定的：当父组件的属性变化时，将传递给子组件，但是不回反过来。修改子组件的prop值，是不会传回给父组件去更新视图的。那么子组件要如何去与父组件通讯呢？
那就是自定义事件。通过在父组件$on(eventName)监听自定义事件，当子组件里$emit(eventName)触发该自定义事件的时候，父组件执行相应的操作。
比如在父组件中控制一个弹框子组件的显示，在子组件中按下关闭之后，告诉父组件去隐藏它，然后父组件就执行操作隐藏弹框。
``` JavaScript
<template>
    <div class="app">
        // hide 为自定义事件，名字可以随便起，不能有大写字母，可以使用短横线
        // @hide 监听子组件触发hide事件，则会执行hideDialog方法
        <dialog :is-show="show" @hide="hideDialog" ></dialog>
        <button @click="showDialog" >显示弹框</button>
    </div>
</template>
<script>
    import dialog from './dialog.vue';
    export default {
        components: { dialog },
        data() {
            return {
                show: false
            }
        },
        methods: {
            showDialog() {
                this.show = true;
            },
            hideDialog() {
                this.show = false;
            }
        }
    }
</script>
```
在子组件dialog.vue中：
``` JavaScript
<template>
    <div class="dialog" v-show="isShow">
        <p>这里是弹框子组件</p>
        <button @click="toHide" >关闭弹框</button>
    </div>
</template>
<script>
    export default {
        // 驼峰式命名的prop需要转换为相对应的短横线隔开式is-show
        props: ['isShow'],
        methods: {
            toHide() {
                // $emit 方法触发父组件的监听事件
                this.$emit('hide');
            }
        }
    }
</script>
```
这就实现了父子组件之间的通信。
### Vuex
上面的例子都是建立在父子关系的组件上，但是对于其他层级的关系，实现起来就比较繁琐了。那么这时候Vuex就能更好的帮你在各个组件间通讯了。
### 总结
组件通讯并不是一定要使用或必须要使用Vuex，对于一些简单的数据传递，prop也可以完成。
原文链接：(http://blog.gdfengshuo.com/2017/07/10)