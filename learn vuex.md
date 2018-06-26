### Learn Vuex

这篇产出完全可以看做是Vuex官网的复刻版，写这篇的目的主要是因为最开始的时候，对Vuex的基础不够熟悉，其它方面也不够深入。现在回头看主要是加深自己的理解，并以文字总结的方式记录下来。

#### 状态模式管理
什么是状态模式管理？以一种集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

一般来说，组件的状态管理一般是自管理模式。
[single-flow](http://p9jftl6n6.bkt.clouddn.com/flow.png)。

但是，如果多个组件都会使用到同一个状态，那么一个组件通过action，对状态state的影响，一定会影响到其它组件的view。这就打破了 **单向数据流** 的模式。

下面是选自Vuex官网一句话，摘抄下来， 是因为它的出发点是理智的。
> 虽然 Vuex 可以帮助我们管理共享状态，但也附带了更多的概念和框架。这需要对 **短期和长期效益**进行权衡。

Vuex是专门为Vue 准备的状态管理工具。

#### 在Vue组件中使用Vuex

创建一个Vuex实例
``` javascript
const store = new Vuex.Store({
    state: {
        count:0
    },

    mutations:{
        increment(state) {
            state.count++;
        }
    }
})
```

获取store实例中所存储状态的方法
- 方法一 通过store实例直接使用字面量的方式访问
```javascript
console.log(store.state.count);
```
- 方法二 Vue提供了store 选项作为Vuex注入的入口，这样在Vue的组件中，就可以通过`this.$store`来访问store实例。
``` javascript
 new Vue({
    el: '#app',
    store:store,
    computed: {
        count:(){
            return this.$store.state.count;
        }
    }
);
```

- 方法三 Vuex提供了mapState 方法，简化方法二的获取方式
``` javascript
new Vue({
    el: '#app',
    store:store,
    data: ()=>{
        localCount: 4
    },
    computed: Vuex.mapState({
        count: state=>state.count,
        countAlias: 'count',
        localState(state){
            return state.count + this.localCount;
        }
}));
```

还有另一种方法：对象展开运算符

#### Vuex中的getter
也许我们需要根据一些条件从state中获取数据，那么这时候我们就可以写getter了。可以把getter看做是state的扩展，再说简单点儿，可以把getter看作是state的filter(并不是完全准确，有助于理解getter的作用)

Vuex如何定义getter？
``` javascript
const store = new Vuex.Store({
    state:{
        length: 5,
        nums: [1,2,3,4,5]
    },

    getters: {
        evenNums: state=>{ // 获取state.nums中的偶数
            return state.nums.filter(n=>{
                return !(n%2);
            })
        }
    }
});

console.log(store.getters.evenNums); // [2,4]
```

#### Vuex中的mutation
注意，注意，Vuex中对state的更改都必须通过mutation来实现。这样做的目的确保 状态变更的途径只有一个，那就是mutation。这样更利于程序调试。

Vuex中定义mutation的方法
```javascript
const store = new Vuex.Store({
    state:{
        length: 5,
        nums: [1,2,3,4,5]
    },

    getters: {
        evenNums: state=>{ // 获取state.nums中的偶数
            return state.nums.filter(n=>{
                return !(n%2);
            })
        }
    },
    mutations: {
        push:state=>{
            state.nums.push(state.nums.length + 1);
        }
    }
});
console.log(store.state.nums); // [1,2,3,4,5]
store.commit('push');
console.log(store.state.nums);// [1,2,3,4,5,6]
```

也可以向mutation传入额外的参数，即载荷payload
``` javascript
const store = new Vuex.Store({
    state:{
        length: 5,
        nums: [1,2,3,4,5]
    },

    getters: {
        evenNums: state=>{ // 获取state.nums中的偶数
            return state.nums.filter(n=>{
                return !(n%2);
            })
        }
    },
    mutations: {
        push:(state, payload)=>{
            state.nums.push(state.nums.length + payload);
        }
    }
});
console.log(store.state.nums); // [1,2,3,4,5]
store.commit('push', 1);
console.log(store.state.nums);// [1,2,3,4,5,6]
store.commit('push', 5);
console.log(store.state.nums);// [1,2,3,4,5,6,11]
```

