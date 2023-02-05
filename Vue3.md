## 1.Vue3简介

- 2020年9月18日，Vue.js发布3.0版本，代号：One Piece（海贼王）
- 耗时2年多、[2600+次提交](https://github.com/vuejs/vue-next/graphs/commit-activity)、[30+个RFC](https://github.com/vuejs/rfcs/tree/master/active-rfcs)、[600+次PR](https://github.com/vuejs/vue-next/pulls?q=is%3Apr+is%3Amerged+-author%3Aapp%2Fdependabot-preview+)、[99位贡献者](https://github.com/vuejs/vue-next/graphs/contributors)
- github上的tags地址：https://github.com/vuejs/vue-next/releases/tag/v3.0.0

## 2.Vue3带来了什么

### 1.性能的提升

- 打包大小减少41%

- 初次渲染快55%, 更新渲染快133%

- 内存减少54%

  ......

### 2.源码的升级

- 使用Proxy代替defineProperty实现响应式

- 重写虚拟DOM的实现和Tree-Shaking

  ......

### 3.拥抱TypeScript

- Vue3可以更好的支持TypeScript

### 4.新的特性

1. Composition API（组合API）

    - setup配置
    - ref与reactive
    - watch与watchEffect
    - provide与inject
    - ......
2. 新的内置组件
    - Fragment
    - Teleport
    - Suspense
3. 其他改变

    - 新的生命周期钩子
    - data 选项应始终被声明为一个函数
    - 移除keyCode支持作为 v-on 的修饰符
    - ......

# 一、创建Vue3.0工程

## 1.使用 vue-cli 创建

官方文档：https://cli.vuejs.org/zh/guide/creating-a-project.html#vue-create

```bash
## 查看@vue/cli版本，确保@vue/cli版本在4.5.0以上
vue --version
## 安装或者升级你的@vue/cli
npm install -g @vue/cli
## 创建
vue create vue3_test
## 启动
cd vue3_test
npm run serve
```

## 2.使用 vite 创建

官方文档：https://v3.cn.vuejs.org/guide/installation.html#vite

vite官网：https://vitejs.cn

- 什么是vite？—— 新一代前端开发与构建工具。
- 优势如下：
   - 开发环境中，无需打包操作，可快速的冷启动。
   - 轻量快速的热重载（HMR）。
   - 真正的按需编译，不再等待整个应用编译完成。
- 传统构建 与 vite构建对比图

<img src="https://cn.vitejs.dev/assets/bundler.37740380.png" style="width:500px;height:280px;float:left" /><img src="https://cn.vitejs.dev/assets/esm.3070012d.png" style="width:480px;height:280px" />

```bash
## 创建工程
npm init vite-app <project-name>
## 进入工程目录
cd <project-name>
## 安装依赖
npm install
## 运行
npm run dev
```

# 二、分析文件目录

## main.js

Vue2项目的main.js

```javascript
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app') // // 将Vue实例对象，挂载到public/index.html的CSS选择器字符串（id: #app）上
```

我们再来看看Vue3项目中的main.js

```js
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

分析一下

```js
// 引入的不再是Vue构造函数了，引入的是一个名为createApp的工厂函数
import { createApp } from 'vue'
import App from './App.vue'

// 创建应用实例对象——app(类似于之前Vue2中的vm，但app比vm更“轻”)
const app = createApp(App)
console.log(app)
// 将app应用实例对象，挂载到public/index.html的CSS选择器字符串（id: #app）上
app.mount('#app')
```

## App.vue

我们再来看看组件

在`template`标签里可以没有根标签了

```html
<template>
	<!-- Vue3组件中的模板结构可以没有根标签 -->
	<img alt="Vue logo" src="./assets/logo.png">
	<HelloWorld msg="Welcome to Your Vue.js App"/>
</template>
```



# 二、常用 Composition API

官方文档: https://v3.cn.vuejs.org/guide/composition-api-introduction.html

## 1.拉开序幕的setup

1. 理解：Vue3.0中一个新的配置项，值为一个函数。

2. setup是所有<strong style="color:#DD5145">Composition API（组合API）</strong><i style="color:gray;font-weight:bold">“ 表演的舞台 ”</i>。

3. 组件中所用到的：数据、方法等等，均要配置在setup中。

5. setup函数的两种返回值：
    1. 若返回一个对象，则对象中的属性、方法, 在模板中均可以直接使用。（常用，重点关注！）
    
       ```javascript
       <template>
         <h1>我是App根组件</h1>
         <h1>我最喜欢的偶像</h1>
         <h3>{{ name }}</h3>
         <h3>{{ age }}</h3>
         <h3>{{ sex }}</h3>
         <h3>{{ a }}</h3>
         <button @click="enjoyMovie($event, '逃学威龙')">
           vue3中setup函数配置--享受电影片刻
         </button>
         <br />
         <br />
         <button @click="singSong('李香兰')">vue2中methods配置--唱歌</button>
       </template>
       
       <script>
       import { h } from "vue";
       // console.log("h", h, typeof h);
       export default {
         name: "App",
         data() {
           return {
             sex: "男",
             a: 666,
           };
         },
         methods: {
           singSong(songName) {
             console.log(this.sex);
             console.log(this.name);
             console.log(this.age);
             console.log(this.enjoyMovie);
             alert(
               `我是${this.name}，年龄${this.age}，性别${this.sex}，唱${songName}`
             );
           },
         },
         setup(props) {
           console.log("setup函数的this指向前", this, typeof this);
           let name = "星爷";
           let age = 17;
           let a = 888;
           function enjoyMovie(e, movieName) {
             console.log("setup函数的this指向后", this, typeof this);
             console.log(name);
             console.log(age);
             console.log(this.sex);
             console.log(this.singSong);
             console.log("e", e, typeof e);
             console.log("movieName", movieName, typeof movieName);
             alert(`我是${name}，年龄${age}，性别${this.sex}，看${movieName}`);
           }
           // 返回一个对象--常用
           return {
             name,
             age,
             enjoyMovie,
             a
           };
         },
       };
       </script>
       
       <style>
       </style>
       ```
    
       
    
    2. <span style="color:#aad">若返回一个渲染函数：则可以自定义渲染内容。（了解）</span>
    
       ```javascript
           // 返回一个渲染函数：则可以自定义渲染内容--了解
           return () => {
             return h("span", "星爷小迷弟");
           };
           // 等价于
           // return () => h("span", "星爷小迷弟");
       ```
    
6. 注意点：
    1. 尽量不要与Vue2.x配置混用
        - Vue2.x配置（data、methos、computed...）中<strong style="color:#DD5145">可以访问到</strong>setup中的属性、方法。
        - 但在setup中<strong style="color:#DD5145">不能访问到</strong>Vue2.x配置（data、methos、computed...）。
        - 如果有重名, setup优先。
    2. setup不能是一个async函数，因为返回值不再是return的对象, 而是Promise实例对象, 模板看不到return对象中的属性。（后期也可以返回一个Promise实例，但需要Suspense和异步组件的配合）


##  2.ref函数

- 作用: 定义一个响应式的数据

- 语法: ```const xxx = ref(initValue)```
    - 创建一个包含响应式数据的<strong style="color:#DD5145">引用实现的实例对象（RefImpl对象，简称ref对象）</strong>。
    
    - JS中读写数据： ```xxx.value```
    
    - 模板中读取数据: 不需要.value，直接：```<div>{{xxx}}</div>``` 原因：Vue3解析模板时，发现xxx是ref对象，会自动读取value属性
    
    - 例子如下：
    
      ```vue
      <template>
        <h1>我最喜欢的偶像</h1>
        <h3>姓名：{{ name }}</h3>
        <h3>年龄：{{ age }}</h3>
        <h3>工作种类：{{ job.type }}</h3>
        <h3>工作薪水：{{job.salary}}</h3>
        <button @click="updateIdolInfo">更新偶像信息</button>
      </template>
      
      <script>
      import { ref } from "vue";
      console.log("ref", ref, typeof ref);
      export default {
        name: "App",
        setup() {
          // let name = "星爷";
          let name = ref("星爷");
          console.log("RefImpl实例对象-name", name.value, typeof name.value);
          let age = ref(17);
          let job = ref({
            type: "前端开发",
            salary: "14k",
          });
          console.log("RefImpl实例对象-age", age.value, typeof age.value);
          console.log("Proxy实例对象-job", job.value, typeof job.value);
          function updateIdolInfo() {
            name.value = "周星星同学";
            age.value = 18;
            job.value.salary = "20k";
            console.log(name.value);
            console.log(age.value);
            console.log("job", job, typeof job.value);
          }
          // 返回一个对象--常用
          return {
            name,
            age,
            job,
            updateIdolInfo,
          };
        },
      };
      </script>
      
      <style>
      </style>
      ```
    
- 备注：
    - 接收的数据可以是：基本类型、也可以是对象类型。
    - 基本类型的数据：响应式依然是靠``Object.defineProperty()``的```get```与```set```完成的。
    - 对象类型的数据：内部 <i style="color:gray;font-weight:bold">“ 求助 ”</i> 了Vue3.0中的一个新函数—— ```reactive```函数，reactive函数内部封装使用了基于 ES6 的Proxy。



## 3.reactive函数

- 作用: 定义一个<strong style="color:#DD5145">对象类型</strong>的响应式数据（注意：基本类型不要用它，要用```ref```函数）
- 语法：```const 代理对象= reactive(源对象)```：接收一个对象（或数组），返回一个<strong style="color:#DD5145">代理对象（Proxy的实例对象，简称proxy对象）</strong>
- reactive定义的响应式数据是“深层次的”。
- 内部基于 ES6 的 Proxy 实现，通过代理对象来操作源对象内部数据。


## 4.Vue3.0中的响应式原理

### vue2.x的响应式

- 实现原理：
    - 对象类型：通过```Object.defineProperty()```对属性的读取、修改进行拦截（数据劫持）。

      ```javascript
      Object.defineProperty(data, 'count', {
          get () {}, 
          set () {}
      })
      ```
    
    - 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组更新元素的方法进行了包裹）。

      对数组更新元素的方法进行了包裹，本质就是做了两件事：
      
      * 调用原生JavaScript对应的方法(比如push方法)对数组进行更新
      * 重新解析模板，进而更新页面
      
      ```
      
      ```
    
    存在问题：
    - 新增属性、删除属性, 界面不会更新。
    - 直接通过索引修改数组, 界面不会自动更新。

### Vue3.0的响应式

- 实现原理:
    - 通过Proxy（代理）:  拦截对象中任意属性的变化, 包括：属性值的读写、属性的添加、属性的删除等。
    - 通过Reflect（反射）:  对源对象的属性进行操作。
    - MDN文档中描述的Proxy与Reflect：
        - Proxy：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy

        - Reflect：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

          ```js
          new Proxy(data, {
              // 拦截读取属性值
              get (target, prop) {
                  return Reflect.get(target, prop)
              },
              // 拦截设置属性值或添加新属性
              set (target, prop, value) {
                  return Reflect.set(target, prop, value)
              },
              // 拦截删除属性
              deleteProperty (target, prop) {
                  return Reflect.deleteProperty(target, prop)
              }
          })
          
          proxy.name = 'tom'   
          ```

## 5.reactive对比ref

-  从定义数据角度对比：
    -  ref用来定义：<strong style="color:#DD5145">基本类型数据</strong>。
    -  reactive用来定义：<strong style="color:#DD5145">对象（或数组）类型数据</strong>。
    -  备注：ref也可以用来定义<strong style="color:#DD5145">对象（或数组）类型数据</strong>, 它内部会自动通过```reactive```转为<strong style="color:#DD5145">代理对象</strong>。
-  从原理角度对比：
    -  ref通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）。
    -  reactive通过使用<strong style="color:#DD5145">Proxy</strong>来实现响应式（数据劫持）, 并通过<strong style="color:#DD5145">Reflect</strong>操作<strong style="color:orange">源对象</strong>内部的数据。
-  从使用角度对比：
    -  ref定义的数据：操作数据<strong style="color:#DD5145">需要</strong>```.value```，读取数据时模板中直接读取<strong style="color:#DD5145">不需要</strong>```.value```。
    -  reactive定义的数据：操作数据与读取数据：<strong style="color:#DD5145">均不需要</strong>```.value```。

## 6.setup的两个注意点

- setup执行的时机
    - 在beforeCreate之前执行一次
    - setup函数的this是undefined。
    
- setup的参数
    - props：值为对象，包含：组件外部传递过来，且组件内部的props配置声明接收了的属性。
    - context：上下文对象
        - attrs: 值为对象，包含：组件外部传递过来，但没有在props配置中声明的属性, 相当于 Vue2的```this.$attrs```。
        - slots: 收到的插槽内容, 相当于 Vue2的```this.$slots```。
        - emit: 触发自定义事件的函数, 相当于 Vue2的```this.$emit```。


## 7.计算属性与侦听

### 1.computed函数

- 与Vue2.x中computed配置功能一致

- 写法

  ```js
  import {computed} from 'vue'
  
  setup(){
      ...
  	//计算属性——简写(没有考虑计算属性被修改的情况 )
      let fullName = computed(()=>{
          return person.firstName + '-' + person.lastName
      })
      //计算属性——完整（考虑读写计算属性）
      let fullName = computed({
          get(){
              return person.firstName + '-' + person.lastName
          },
          set(value){
              const nameArr = value.split('-')
              person.firstName = nameArr[0]
              person.lastName = nameArr[1]
          }
      })
  }
  ```

### 2.watch函数

- 与Vue2.x中watch配置功能一致

- 两个小“坑”：

    - 侦听reactive定义的响应式数据时：oldValue无法正确获取、强制开启了深度侦听（deep配置失效）。
    - 侦听reactive定义的响应式数据中某个属性时：deep配置有效。

  ```js
  //情况一：侦听ref定义的一个响应式数据
  watch(sum,(newValue,oldValue)=>{
  	console.log('sum变化了',newValue,oldValue)
  },{immediate:true})
  
  //情况二：侦听ref定义的多个响应式数据
  watch([sum,msg],(newValue,oldValue)=>{
  	console.log('sum或msg变化了',newValue,oldValue)
  }) 
  
  /* 情况三：侦听reactive定义的响应式数据
  			1.若watch侦听的是reactive定义的响应式数据，则无法正确获取oldValue！！
  			2.若watch侦听的是reactive定义的响应式数据，则强制开启了深度侦听（deep配置无效）
  */
  watch(person,(newValue,oldValue)=>{
  	console.log('person变化了',newValue,oldValue)
  },{immediate:true,deep:false}) //此处的deep配置不再奏效
  
  //情况四：侦听reactive定义的响应式数据中的某个属性
  watch(()=>person.age,(newValue,oldValue)=>{
  	console.log('person的age变化了',newValue,oldValue)
  },{immediate:true,deep:true}) 
  
  //情况五：侦听reactive定义的响应式数据中的某些属性
  watch([()=>person.name,()=>person.age],(newValue,oldValue)=>{
  	console.log('person的name或age变化了',newValue,oldValue)
  },{immediate:true,deep:true})
  
  // 情况六：侦听reactive定义的响应式数据中的某个对象
  
  watch(()=>person.job,(newValue,oldValue)=>{
  // watch侦听的是reactive定义的响应式数据，则无法正确获取oldValue
      console.log('person的job变化了',newValue,oldValue)
  },{deep:true}) //此处由于侦听的是reactive定义的响应式数据中的某个对象，所以deep配置有效
  ```

注意事项：

​	1.基本数据类型sum是使用ref定义的响应式数据，不能直接侦听sum.value，因为sum是RefImpl引用实现实例对象，sum.value取的是值，所以侦听的是sum（侦听sum的数据结构，sum的数据结构是RefImpl实例对象）

​	2.引用数据类型idolInfo是使用ref定义的响应式数据，如果直接侦听indolInfo，name或age值发生变化了，不会侦听到idolInfo的变化。

原因：因为idolInfo是RefImpl实例对象，name或age值发生变化，只是第二层的数据变化，而idolInfo.value指向的Proxy对象的最外层的内存地址没有改变，所以不会侦听到idolInfo的变化

解决有2种方法：

​		方法一：使用idolInfo.value获取Proxy实例对象（底层借助了reactive函数来定义响应式数据），Proxy实例对象是强制开启了深度侦听

​		方法二：idolInfo是RefImpl实例对象，使用deep: truek开启深度侦听

### 3.watchEffect函数

- watch的套路是：既要指明侦听的属性，也要指明侦听的回调。

- watchEffect的套路是：不用指明侦听哪个属性，侦听的回调中用到哪个属性，那就侦听哪个属性。

- watchEffect有点像computed：

    - 但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值。
    - 而watchEffect更注重的是过程（回调函数的函数体），所以不用写返回值。

  ```js
  //watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
  watchEffect(()=>{
      const x1 = sum.value
      const x2 = person.age
      console.log('watchEffect配置的回调执行了')
  })
  ```
## 8.生命周期

<div style="border:1px solid black;width:380px;float:left;margin-right:20px;"><strong>vue2.x的生命周期</strong><img src="https://cn.vuejs.org/images/lifecycle.png" alt="lifecycle_2" style="zoom:33%;width:1200px" /></div><div style="border:1px solid black;width:510px;height:985px;float:left"><strong>vue3.0的生命周期</strong><img src="https://v3.cn.vuejs.org/images/lifecycle.svg" alt="lifecycle_2" style="zoom:33%;width:2500px" /></div>

































1

- Vue3.0中可以继续使用Vue2.x中的生命周期钩子，但有有两个被改名：
    - ```beforeDestroy```改名为 ```beforeUnmount```
    - ```destroyed```改名为 ```unmounted```
- Vue3.0也提供了 Composition API 形式的生命周期钩子，与Vue2.x中钩子对应关系如下：
    - `beforeCreate`===>`setup()`
    - `created`=======>`setup()`
    - `beforeMount` ===>`onBeforeMount`
    - `mounted`=======>`onMounted`
    - `beforeUpdate`===>`onBeforeUpdate`
    - `updated` =======>`onUpdated`
    - `beforeUnmount` ==>`onBeforeUnmount`
    - `unmounted` =====>`onUnmounted`


## 9.自定义hook函数

- 什么是hook？—— 本质是一个函数，把setup函数中使用的Composition API进行了封装。

- 类似于vue2.x中的mixin。

- 自定义hook的优势: 复用代码, 让setup中的逻辑更清楚易懂。


## 10.toRef和toRefs

- 作用：创建一个 ref 对象，其value属性值指向另一个对象中的某个属性。
- 语法：```const name = toRef(person,'name')```
- 原理：比如*name: toRef(idolInfo, "name")*，toRef函数获取的ObjectRefImpl实例对象，其value属性通过get函数，指向原来响应式对象的对应属性name，存在引用关系
- 应用:   要将响应式对象中的某个属性，单独提供给外部使用时。


- 扩展：```toRefs``` 与```toRef```功能一致，但可以批量创建多个 ref 对象，语法：```toRefs(person)```


# 三、其它 Composition API

## 1.shallowReactive 与 shallowRef

- shallowReactive：只处理对象最外层属性的响应式（浅层响应式）。
- shallowRef：只处理基本数据类型的响应式, 不处理对象的响应式。

- 应用场景，什么时候使用?
    -  如果有一个对象数据，结构比较深, 但变化时只是外层属性变化 ===> shallowReactive。
    -  如果有一个对象数据，后续功能不会修改该对象中的属性，而是生成新的对象来替换 ===> shallowRef。


## 2.readonly 与 shallowReadonly

- readonly: 让一个响应式数据变为只读的（深层只读）。
- shallowReadonly：让一个响应式数据变为只读的（浅层只读）。
- 应用场景: 不希望数据被修改时，比如这个数据是其他组件传过来的。


## 3.toRaw 与 markRaw

- toRaw：
    - 作用：将一个由```reactive```定义的<strong style="color:orange">响应式对象</strong>转为原始的<strong style="color:orange">普通对象</strong>。（注意：ref定义的响应式数据不适用，得到的是*RefImpl实例对象*）
    - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新。
- markRaw：
    - 作用：标记一个对象，使其永远不会再成为响应式对象。
    - 应用场景:
        1. 有些值不应被设置为响应式的，例如复杂的第三方类库等，因为第三方类库的对象存在复杂庞大的属性，Vue3底层要针对这些属性，设置成响应式的，显然是浪费性能。
        2. 当渲染具有不可变数据源的大列表时，跳过设置响应式，可以提高性能。



## 4.customRef

- 作用：创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。

- 实现防抖效果：

  ```vue
  <template>
  	<input type="text" v-model="keyword">
  	<h3>{{keyword}}</h3>
  </template>
  
  <script>
  	import {ref,customRef} from 'vue'
  	export default {
  		name:'Demo',
  		setup(){
  			// let keyword = ref('hello') //使用Vue准备好的内置ref
  			//自定义一个myRef
  			function myRef(value,delay){
  				let timer
  				//通过customRef去实现自定义
  				return customRef((track,trigger)=>{
  					return{
  						get(){
  							track() //告诉Vue这个value值是需要被“追踪”的
  							return value
  						},
  						set(newValue){
  							clearTimeout(timer)
  							timer = setTimeout(()=>{
  								value = newValue
  								trigger() //告诉Vue去更新界面
  							},delay)
  						}
  					}
  				})
  			}
  			let keyword = myRef('hello',500) //使用程序员自定义的ref
  			return {
  				keyword
  			}
  		}
  	}
  </script>
  ```

## 5.provide 与 inject

<img src="https://v3.cn.vuejs.org/images/components_provide.png" style="width:300px" />

- 作用：实现<strong style="color:#DD5145">祖与后代组件间</strong>通信

- 套路：祖组件有一个 `provide` 选项来提供数据，后代组件有一个 `inject` 选项来使用这些数据

- 具体写法：

    1. 祖组件中：

       ```js
       setup(){
           ......
           let car = reactive({name:'奔驰',price:'40万'})
           provide('car',car)
           ......
       }
       ```

    2. 后代组件中：

       ```js
       setup(props,context){
           ......
           const car = inject('car')
           return {car}
           ......
       }
       ```
## 6.响应式数据的判断

- isRef: 检查一个值是否为一个 ref 对象
- isReactive: 检查一个对象是否是由 `reactive` 创建的响应式代理对象
- isReadonly: 检查一个对象是否是由 `readonly` 创建的只读代理对象
- isProxy: 检查一个对象是否是由 `reactive` 或者 `readonly` 方法创建的代理 对象 (说明：reactive和readonly定义的对象是代理对象，是响应式对象)


# 四、Composition API 的优势

## 1.Options API 存在的问题

使用传统OptionsAPI中，新增或者修改一个需求，就需要分别在data，methods，computed里修改 。

<div style="width:600px;height:370px;overflow:hidden;float:left">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f84e4e2c02424d9a99862ade0a2e4114~tplv-k3u1fbpfcp-watermark.image" style="width:600px;float:left" />
</div>
<div style="width:300px;height:370px;overflow:hidden;float:left">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5ac7e20d1784887a826f6360768a368~tplv-k3u1fbpfcp-watermark.image" style="zoom:50%;width:560px;left" /> 
</div>











## 2.Composition API 的优势

我们可以更加优雅的组织我们的代码，函数。让相关功能的代码更加有序的组织在一起。

<div style="width:500px;height:340px;overflow:hidden;float:left">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc0be8211fc54b6c941c036791ba4efe~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>
<div style="width:430px;height:340px;overflow:hidden;float:left">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6cc55165c0e34069a75fe36f8712eb80~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>




# 五、新的组件

## 1.Fragment

- 在Vue2中: 组件必须有一个根标签
- 在Vue3中: 组件可以没有根标签, 内部会将多个标签包含在一个Fragment组件（或者叫虚拟元素）中
- 好处: 减少标签层级, 减小内存占用

## 2.Teleport

- 什么是Teleport？—— `Teleport` 是一种能够将我们的<strong style="color:#DD5145">组件的html结构</strong>移动到指定位置（可以是body标签或CSS选择器字符串等）的技术。简称“传送”

  ```vue
      <!-- 使用teleport组件，传送挂载在body标签或index.html的CSS选择器字符串下 -->
  <teleport to="移动位置">
  	<div v-if="isShow" class="mask">
  		<div class="dialog">
  			<h3>我是一个弹窗</h3>
  			<button @click="isShow = false">关闭弹窗</button>
  		</div>
  	</div>
  </teleport>
  ```

## 3.Suspense
- 等待异步组件时渲染一些额外内容，让应用有更好的用户体验

- 使用步骤：

    - 异步引入组件

      ```js
      import {defineAsyncComponent} from 'vue'
      const Child = defineAsyncComponent(()=>import('./components/Child.vue'))
      ```

    - 使用```Suspense```包裹组件，并配置好```default``` 与 ```fallback```

      ```vue
      <template>
          <div class="app">
              <h3>我是App组件</h3>
              <Suspense>
                  <template v-slot:default>
                      <Child/>
                  </template>
                  <template v-slot:fallback>
                      <h3>加载中.....</h3>
                  </template>
              </Suspense>
          </div>
      </template>
      ```

​	说明：使用静态引入，如果组件嵌套多层时，即组件内嵌套组件，会等最内层的组件渲染完成后，页面才会显示。相当于生活中的经典案例：木桶短板


# 六、其他

## 1.全局API的转移

- Vue 2.x 有许多全局 API 和配置。

    - 例如：注册全局组件、注册全局指令等。

      ```js
      //注册全局组件
      Vue.component('MyButton', {
        data: () => ({
          count: 0
        }),
        template: '<button @click="count++">Clicked {{ count }} times.</button>'
      })
      
      //注册全局指令
      Vue.directive('focus', {
        inserted: el => el.focus()
      }
      ```

- Vue3.0中对这些API做出了调整：

    - 将全局的API，即：```Vue.xxx```调整到应用实例（```app```）上

      | 2.x 全局 API（```Vue```） | 3.x 实例 API (`app`)                        |
                | ------------------------- | ------------------------------------------- |
      | Vue.config.xxxx           | app.config.xxxx                             |
      | Vue.config.productionTip  | <strong style="color:#DD5145">移除</strong> |
      | Vue.component             | app.component                               |
      | Vue.directive             | app.directive                               |
      | Vue.mixin                 | app.mixin                                   |
      | Vue.use                   | app.use                                     |
      | Vue.prototype             | app.config.globalProperties                 |

## 2.其他改变

- data选项应始终被声明为一个函数。

- 过度类名的更改：

    - Vue2.x写法

      ```css
      .v-enter,
      .v-leave-to {
        opacity: 0;
      }
      .v-leave,
      .v-enter-to {
        opacity: 1;
      }
      ```

    - Vue3.x写法

      ```css
      .v-enter-from,
      .v-leave-to {
        opacity: 0;
      }
      
      .v-leave-from,
      .v-enter-to {
        opacity: 1;
      }
      ```

- <strong style="color:#DD5145">移除</strong>keyCode作为 v-on 的修饰符，同时也不再支持```config.keyCodes```

- <strong style="color:#DD5145">移除</strong>```v-on.native```修饰符

    - 父组件中绑定事件

      ```vue
      <my-component
        v-on:close="handleComponentEvent"
        v-on:click="handleNativeClickEvent"
      />
      ```

    - 子组件中声明自定义事件

      ```vue
      <script>
        export default {
          emits: ['close']
        }
      </script>
      ```

- <strong style="color:#DD5145">移除</strong>过滤器（filter）

  > 过滤器虽然这看起来很方便，但它需要一个自定义语法，打破大括号内表达式是 “只是 JavaScript” 的假设，这不仅有学习成本，而且有实现成本！建议用方法调用或计算属性去替换过滤器。

- ......



