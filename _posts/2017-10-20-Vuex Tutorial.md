---
title: Vuex Tutorial
date: 2017-10-20
description: 干活满满，说人话的vuex入门教程。
categories:
 - Vuex
 - frame
photos: http://oxpvb4fav.bkt.clouddn.com/15084657128175.jpg
tags: Vuex 框架 案例
---

>本文介绍了vue-x的基本用法，案例参考The Net Ninja的youtube视频。如有不足，请多指教。同样学习之前需要您已经掌握vue和es6的基本用法。


## 创建一个项目

>首先需要安装vue-cli创建vue和vuex的运行环境

### 我们要创建什么项目？

我们需要创建的是两个商品列表，分别展示各自的商品名称和价格。并且具备更改价格的方法。

![](http://oxpvb4fav.bkt.clouddn.com/15084647697113.jpg)


### 用vue进行内容创建

首先我们先用vue进行内容创建，我们应当有两个商品列表，所以创建了ProductListOne和ProductListTwo两个组件。

既然是vue的创建方式，所以我们首先用的传值方式还是props。先把所有数据放在父组件App.vue中，再通过props传递给子组件。

```js
//App.vue
<template>
  <div id="app">
    <product-list-one :products="products"></product-list-one>
    <product-list-two :products="products"></product-list-two>
  </div>
</template>

<script>
  import ProductListOne from './components/ProductListOne.vue'
  import ProductListTwo from './components/ProductListTwo.vue'

  export default{
    components: {
        'product-list-one':ProductListOne,
        'product-list-two':ProductListTwo
    },
    name: 'app',
    data(){
      return {
        products: [
          {name: 'Banana Skin', price: 20},
          {name: 'Shiny Star', price: 40},
          {name: 'Green Shells', price: 60},
          {name: 'Red Shells', price: 80}
        ]
      }
    }

  }
</script>
```

```js
//ProductListOne
<template>
  <div id="product-list-one">
    <h2>Product List One</h2>
    <ul>
      <li v-for="product in products" :key="index">
        <span class="name">{{product.name}}</span>
        <span class="price">${{product.price}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
  export default {
    props: ['products'],
    data(){
      return {}
    }
  }
</script>

<style>
</style>
```

```js
//ProductListTwo
<template>
    <div id="product-list-two">
        <h2>Product List Two</h2>
        <ul>
            <li v-for="product in products" :key="index">
                <span class="name">{{product.name}}</span>
                <span class="price">${{product.price}}</span>
            </li>
        </ul>

    </div>
</template>

<script>
  export default {
    props: ['products'],
    data(){
      return {}
    }
  }
</script>

<style >

</style>
```

### 预览截图

![](http://oxpvb4fav.bkt.clouddn.com/15084185906840.jpg)


## central Store

>接下来我们看看用vuex怎么管理我们的数据。

1. 首先我们需要安装vuex

```js
npm install --save-dev vuex
```

2. 创建store/index.js

3. 把App.vue的数据移到index.js

```js
//App.vue
<template>
  <div id="app">
    <product-list-one :products="products"></product-list-one>
    <product-list-two :products="products"></product-list-two>
  </div>
</template>

<script>
  import ProductListOne from './components/ProductListOne.vue'
  import ProductListTwo from './components/ProductListTwo.vue'

  export default{
    components: {
        'product-list-one':ProductListOne,
        'product-list-two':ProductListTwo
    },
    name: 'app',
    data(){
      return {
      }
    }

  }
</script>
```

```js
//store/index.js
import Vuex from 'vuex'
import Vue from 'vue'

Vue.use(Vuex) // 不写这句话浏览器控制台就会报错，于是我就写了

export const store = new Vuex.Store({
  state: {
    products: [
      {name: 'Banana Skin', price: 20},
      {name: 'Shiny Star', price: 40},
      {name: 'Green Shells', price: 60},
      {name: 'Red Shells', price: 80}
    ]
  }
})
```

```js
//修改main.js
import Vue from 'vue'
import App from './App.vue'
import {store} from './store/index.js'

/* eslint-disable no-new */
new Vue({
  store:store,
  el: '#app',
  render: h=>h(App)
})
```

```js
//ProductListOne
<template>
  <div id="product-list-one">
    <h2>Product List One</h2>
    <ul>
      <li v-for="product in products">
        <span class="name">{{product.name}}</span>
        <span class="price">${{product.price}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
  export default {
      //getCentral store,use computed attribute
    //when we call products,we use the computed method products that return the products in the store's state
    computed:{
      products(){
          return this.$store.state.products;
      }
    }
  }
</script>
```
```js
//ProductListTwo
<template>
    <div id="product-list-two">
        <h2>Product List Two</h2>
        <ul>
            <li v-for="product in products">
                <span class="name">{{product.name}}</span>
                <span class="price">${{product.price}}</span>
            </li>
        </ul>

    </div>
</template>

<script>
  export default {
    //getCentral store,use computed attribute
    //when we call products,we use the computed method products that return the products in the store's state
    computed:{
      products(){
        return this.$store.state.products;
      }
    }
  }
</script>
```

### 预览
![](http://oxpvb4fav.bkt.clouddn.com/15084197311045.jpg)

## Getters

>假设一个需求，我们需要对上架的商品进行打折处理。就是我们上述的改变价格的方法。

我们可以使用computed来处理products，新建一个saleProducts变量，作为处理后的值。

```js
//ProductListOne
<template>
  <div id="product-list-one">
    <h2>Product List One</h2>
    <ul>
      <li v-for="product in saleProducts">
        <span class="name">{{product.name}}</span>
        <span class="price">${{product.price}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
  export default {
    //getCentral store,use computed attribute
    //when we call products,we use the computed method products that return the products in the store's state
    computed: {
      products(){
        return this.$store.state.products;
      },
      saleProducts(){
        var saleProducts = this.$store.state.products.map((product) => {
          return {
            name: '**' + product.name + '**',
            price: product.price / 2
          }
        })
        return saleProducts;
      }
    }
  }
</script>
```

### 预览：

>注意ProductListTwo我们没有处理

![](http://oxpvb4fav.bkt.clouddn.com/15084202786760.jpg)

### 如果我们想要这种saleProducts也利用在其他地方呢？

不可能每个组件都复制这个函数吧

所以我们可以把它作为store的一个getter，output在我们需要的地方

```js
//store/index.js

import Vuex from 'vuex'
import Vue from 'vue'

Vue.use(Vuex) // 不写这句话浏览器控制台就会报错，于是我就写了

export const store = new Vuex.Store({
  state: {
    products: [
      {name: 'Banana Skin', price: 20},
      {name: 'Shiny Star', price: 40},
      {name: 'Green Shells', price: 60},
      {name: 'Red Shells', price: 80}
    ]
  },
  //由于我们在store里，所以不用写this.$store，直接把state传进去就ok了
  getters:{
    saleProducts:state=>{
      var saleProducts = state.products.map((product) => {
        return {
          name: '**' + product.name + '**',
          price: product.price / 2
        }
      })
      return saleProducts;
    }
  }
})
```

而在组件中，直接调用```this.$store.getters.saleProducts```就可以了。

```js
//ProductListOne
<template>
  <div id="product-list-one">
    <h2>Product List One</h2>
    <ul>
      <li v-for="product in saleProducts">
        <span class="name">{{product.name}}</span>
        <span class="price">${{product.price}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
  export default {
    //getCentral store,use computed attribute
    //when we call products,we use the computed method products that return the products in the store's state
    computed: {
      products(){
        return this.$store.state.products;
      },
      saleProducts(){
          return this.$store.getters.saleProducts
      }
    }
  }
</script>
```

### 预览
>注意ProductListTwo没有更改
![](http://oxpvb4fav.bkt.clouddn.com/15084208347716.jpg)


## Mutations

>how to change the state in the store?

假设我们需要点击来改变价格。
我们首先想到的是创建一个按钮，点击时触发method来改变this.$store.product。
来试试看是否可行。

```js
//ProductListOne
<template>
  <div id="product-list-one">
    <h2>Product List One</h2>
    <ul>
      <li v-for="product in saleProducts">
        <span class="name">{{product.name}}</span>
        <span class="price">${{product.price}}</span>
      </li>
    </ul>
    <button @click="reducePrice">Reduce Price</button>
  </div>
</template>

<script>
  export default {
    //getCentral store,use computed attribute
    //when we call products,we use the computed method products that return the products in the store's state
    computed: {
      products(){
        return this.$store.state.products;
      },
      saleProducts(){
          return this.$store.getters.saleProducts
      }
    },
    methods:{
        reducePrice:function () {
          this.$store.state.products.forEach(product=>{
              product.price -= 1;
          })
        }
    }
  }
</script>

```

**注意：**如果在index.js的state前加```use:strict,```表示使用严格模式，那么这样直接修改state是不允许的（只能通过mutation修改）

### 预览
![](http://oxpvb4fav.bkt.clouddn.com/15084212360402.jpg)

虽然是可以更改了，但是有一个问题，就是我们不知道是谁改变了state，我们现在自己写当然知道，要是从外部看似乎看不出来。

### 所以我们需要用mutation，可以追踪变化发生的源头。

这就是mutation的作用，可以让我们追溯数据更改的源头，并且在vue dev-tool中可以看到。

cool，所以我们在store/index.js中创建这个mutations（可能有多个mutation，所以这里是复数）

**注意**
由于我们在index.js中操作，所以不需要写this.$store了，需要state时，直接写state。

```js
//store/index.js
import Vuex from 'vuex'
import Vue from 'vue'

Vue.use(Vuex) // 不写这句话浏览器控制台就会报错，于是我就写了

export const store = new Vuex.Store({
  state: {
    products: [
      {name: 'Banana Skin', price: 20},
      {name: 'Shiny Star', price: 40},
      {name: 'Green Shells', price: 60},
      {name: 'Red Shells', price: 80}
    ]
  },
  getters: {
    saleProducts: state => {
      var saleProducts = state.products.map((product) => {
        return {
          name: '**' + product.name + '**',
          price: product.price / 2
        }
      })
      return saleProducts;
    }
  },
  //mutations可以通过vue-devtool进行跟踪
  mutations: {
    reducePrice: state => {
      state.products.forEach(product => {
        product.price -= 1;
      })
    }
  }
})
```

子组件如何调用呢？就是commit方法。直接commit('mutation'),
mutation就是我们具体在index.js里创建的mutation名称。

```js
//ProductListOne
<template>
  <div id="product-list-one">
    <h2>Product List One</h2>
    <ul>
      <li v-for="product in saleProducts">
        <span class="name">{{product.name}}</span>
        <span class="price">${{product.price}}</span>
      </li>
    </ul>
    <button @click="reducePrice">Reduce Price</button>
  </div>
</template>

<script>
  export default {
    computed: {
      products(){
        return this.$store.state.products;
      },
      saleProducts(){
          return this.$store.getters.saleProducts
      }
    },
    methods:{
        reducePrice:function () {
          this.$store.commit('reducePrice')
          //reducePrice是我们定义在store里的mutations里的方法，要用commit触发
        }
    }
  }
</script>
```

### 预览

![](http://oxpvb4fav.bkt.clouddn.com/15084221864742.jpg)


## Actions

使用mutations时有一个问题需要注意，就是不要直接使用异步，比如从服务器加载数据.

我们把index.js的mutation更改一下，用setTimeout来模拟异步.看看会发生什么事。

```js
 mutations: {
    reducePrice: state => {
      setTimeout(function () {
        state.products.forEach(product => {
            product.price -= 1;
          }
        )
      },3000)
    }
  }
```

### 预览

![](http://oxpvb4fav.bkt.clouddn.com/15084227973293.jpg)

### 解决办法就是使用actions，可以拦截到达mutation的指令

#### 1.建立一个actions，把异步获取的方法放在actions

```js
//index.js
  mutations: {
    reducePrice: state => {
      state.products.forEach(product => {
        product.price -= 1;
      })
    }
  },
  actions:{
    //context是上下文，这里类似store
    reducePrice:context=>{
      setTimeout(function () {
        context.commit('reducePrice')
      },2000)
    }
  }
```

#### 2.组件不直接触发mutation,而是通过dispatch一个action，让action来触发mutation，这样数据到来后，mutation才会被触发，我们也就可以实时看到变化。

```js
//ProductListOne
<template>
  <div id="product-list-one">
    <h2>Product List One</h2>
    <ul>
      <li v-for="product in saleProducts">
        <span class="name">{{product.name}}</span>
        <span class="price">${{product.price}}</span>
      </li>
    </ul>
    <button @click="reducePrice">Reduce Price</button>
  </div>
</template>

<script>
  export default {
    computed: {
      products(){
        return this.$store.state.products;
      },
      saleProducts(){
          return this.$store.getters.saleProducts
      }
    },
    methods:{
        reducePrice:function () {
          this.$store.dispatch('reducePrice')
        }
    }
  }
</script>
```

#### 3.预览
![](http://oxpvb4fav.bkt.clouddn.com/15084233992850.jpg)

### 假设我们每次减价时，不是固定的值，那么我们就需要一个payload，作为参数传递。

#### 1.我们需要在click的时候直接传入这个参数，比如是4，所以我们先在button这里改

```html
//ProductListOne.js
 <button @click="reducePrice(4)">Reduce Price</button>
```

#### 2.既然这个reducePrice触发的是dispatch，我们dispatch也要改。

>实际上就是在function直接传一个payload而已

```js
//ProductListOne.js
  methods:{
        reducePrice:function (payload) {
          this.$store.dispatch('reducePrice',payload)
        }
    }
```

#### 3.既然dispatch改了，我们的action和mutation也要改。

```js
//store/index.js

  mutations: {
    reducePrice: (state,payload) => {
      state.products.forEach(product => {
        product.price -= payload;
      })
    }
  },
  actions:{
    //context是上下文，这里类似store
    reducePrice:(context,payload)=>{
      setTimeout(function () {
        context.commit('reducePrice',payload)
      },2000)
    }
  }
```

这样每次点击都减4了。

### 预览

![](http://oxpvb4fav.bkt.clouddn.com/15084240168928.jpg)

## Mapping Actions & Getters

>如果我们有很多个getters和actions怎么办？难道在每一个都在computed里用函数调用吗？
这样是不是有点繁琐？

我们可以用mapping来解决。

### 引入mapActions和mapGetters

```js
//ProductListOne.js
import {mapActions,mapGetters} from 'vuex'
```
然后使用

```js
<script>
  import {mapActions,mapGetters} from 'vuex'
  export default {
    computed: {
      products(){
        return this.$store.state.products;
      },
      ...mapGetters([
        'saleProducts'
//        'yourNextOne'
      ])
    },
    methods:{
      ...mapActions([
          'reducePrice'
//         'yourNextOne'
      ])
    }
  }
</script>
```

### 注意
如果此时浏览器无法识别我们的es6语法，比如...mapActions，我们需要安装babel

```js
npm install babel-preset-stage-2 --save-dev
```

```js
//.babelrc    配置
{
  "presets":[
    ["env",{"modules":false}],
    ["stage-2"]
  ]
}
```

结果当然是不变的。只不过当我们有多个action和getter的时候，调用更加方便。

![](http://oxpvb4fav.bkt.clouddn.com/15084657128175.jpg)

### 源代码：

[myGitHub](https://github.com/80666881/vuex-tutorial)

#### usage

```
git clone git@github.com:80666881/vuex-tutorial.git
```

```
npm install
```

```
npm start
```











