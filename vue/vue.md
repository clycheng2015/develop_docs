## 基础语法 ##
1、vue指令
> v-bind：（attributeName）
> v-on：click（事件名）事件监听
> v-if
> v-model：（input、select、text、checkbox、radio 等输入框中我们可以使用 v-model 指令来实现双向数据绑定），作用在一个函数上
> v-show：类似v-if条件渲染，其作用的元素始终会被渲染并保留在 DOM 中，只是简单地切换元素的 CSS 属性 display，v-show 不支持 <template> 元素，也不支持 v-else。
> v-html
> 代替slot 和 slot-scope 的使用
> v-cloak：隐藏未编译的变量，直到 Vue 实例准备就绪


2、指令缩写
	
    <a v-bind:href="url">...</a>   //完整语法
    <a :href="url">...</a>         //缩写

    <!-- 完整语法 -->
    <a v-on:click="doSomething">...</a>
    
    <!-- 缩写 -->
    <a @click="doSomething">...</a>

3、Vue属性
> el:绑定数据的dom节点
> data：绑定待观察待改变的数据
> computed：计算属性（对象），定义通用的方法，在dom中可以多次复用，依赖于待监听的函数并缓存结果，不会每次执行（只有依赖改变时才执行）。，可以定义getter、setter
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
使用：vm.fullName = 'John Doe' 时，setter 会被调用，vm.firstName 和 vm.lastName 也会相应地被更新。
> watch 侦听属性：watch(与计算属性类似)
> methods：常规方法，可以使用在vue节点元素上，也可以用 JavaScript 直接调用方法
> created：生命周期方法
> router：路由表
> mixins：混入对象
> components：局部注册组件
> filters：过滤器函数，接受表达式的值作为第一个参数，切过滤器函数可以串联，示例：

	<div id="app">
	  {{ message | capitalize }}
	</div>
	    
	<script>
	new Vue({
	  el: '#app',
	  data: {
	    message: 'runoob'
	  },
	  filters: {
	    capitalize: function (value) {
	      if (!value) return ''
	      value = value.toString()
	      return value.charAt(0).toUpperCase() + value.slice(1)
	    }
	  }
	})
	</script>
	//过滤器函数串联
    {{ message | filterA | filterB }}

>watch:监听属性，1、vm.$watch监听某个属性的变化2、类似于 watch{func1(){},func2(){}}形式，监听方法的变化（方法内控制属性的变化）

4、key 属性 ，唯一标记一个组件，默认，没有使用key的同类组件会在渲染时被复用。

**5、变异方法----会改变被这些方法调用的原始数组**
使用：example1.items.push({ message: 'Baz' })
> push()
> pop()
> shift()
> unshift()
> splice()
> sort()
> reverse()

**6、非变异---替换数组---这些不会改变原始数组，但总是返回一个新数组**
filter()
concat()
slice()
**
7、对象更改检测注意事项**
1、Vue 不能检测以下变动的数组
> **注意1**：当你利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue
> **注意2**：当你修改数组的长度时，例如：vm.items.length = newLength


实现方法：

解决上面问题一

> // Vue.set
> Vue.set(vm.items, indexOfItem, newValue)
> // Array.prototype.splice
> vm.items.splice(indexOfItem, 1, newValue)


解决上面问题二
> vm.items.splice(newLength)

**注意3**：Vue 不能检测对象属性的添加或删除
> var vm = new Vue({
>   data: {
> a: 1
>   }
> })
> // `vm.a` 现在是响应式的
> 
> vm.b = 2
> // `vm.b` 不是响应式的

解决方式：
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})

Vue.set(vm.userProfile, 'age', 27)
或：
vm.$set(vm.userProfile, 'age', 27)
**
注意4**：
有时你可能需要为已有对象赋予多个新属性，
**错误方式**：
> Object.assign(vm.userProfile, {
>   age: 27,
>   favoriteColor: 'Vue Green'
> })


正确方式（应该用两个对象的属性创建一个新的对象）
> vm.userProfile = Object.assign({}, vm.userProfile, {
>   age: 27,
>   favoriteColor: 'Vue Green'
> })

## 8、事件修饰符 ##
v-on常见事件
> .stop
> .prevent
> .capture
> .self
> .once
> .passive

	<!-- 阻止单击事件继续传播 -->
	<a v-on:click.stop="doThis"></a>
	
	<!-- 提交事件不再重载页面 -->
	<form v-on:submit.prevent="onSubmit"></form>
	
	<!-- 修饰符可以串联 -->
	<a v-on:click.stop.prevent="doThat"></a>
	
	<!-- 只有修饰符 -->
	<form v-on:submit.prevent></form>
	
	<!-- 添加事件监听器时使用事件捕获模式 -->
	<!-- 即元素自身触发的事件先在此处理，然后才交由内部元素进行处理 -->
	<div v-on:click.capture="doThis">...</div>
	
	<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
	<!-- 即事件不是从内部元素触发的 -->
	<div v-on:click.self="doThat">...</div>
	<!-- 点击事件将只会触发一次 -->
	<a v-on:click.once="doThis"></a>

	<!-- 这个 .passive 修饰符尤其能够提升移动端的性能。-->
	<div v-on:scroll.passive="onScroll">...</div>

注意：
1、
使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，
用 v-on:click.prevent.self 会阻止所有的点击，
而 v-on:click.self.prevent 只会阻止对元素自身的点击。
2、
不要把 .passive 和 .prevent 一起使用，因为 .prevent 将会被忽略，
同时浏览器可能会向你展示一个警告。请记住，.passive 会告诉浏览器你不想阻止事件的默认行为。

## 9、按键修饰符 ##
	<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
	<input v-on:keyup.enter="submit">

按键码：
> .enter
> .tab
> .delete (捕获“删除”和“退格”键)
> .esc
> .space
> .up
> .down
> .left
> .right

系统修饰键
> .ctrl
> .alt
> .shift
> .meta（不同键盘对应的标识可能不一样）


.exact 修饰符---允许你控制由精确的系统修饰符组合触发的事件

	<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
	<button @click.ctrl="onClick">A</button>
	
	<!-- 有且只有 Ctrl 被按下的时候才触发 -->
	<button @click.ctrl.exact="onCtrlClick">A</button>
	
	<!-- 没有任何系统修饰符被按下的时候才触发 -->
	<button @click.exact="onClick">A</button>

10、表单输入框
1、v-model 在内部使用不同的属性为不同的输入元素并抛出不同的事件：
> text 和 textarea 元素使用 value 属性和 input 事件；
> checkbox 和 radio 使用 checked 属性和 change 事件；
> select 字段将 value 作为 prop 并将 change 作为事件。

2、在文本区域插值 (<textarea></textarea>) 并不会生效，应用 v-model 来代替。
3、值绑定
（不理解。。。。。。。。。。。。。。。。。。。。。。。。。。。。。）
4、修饰符
> .lazy：延迟数据同步，默认在input 事件进行数据同步，修饰后监听change 事件时进行数据同步。
**input v-model.lazy="msg" **
> .number：如果想自动将用户的输入值转为数值类型，可以给 v-model 添加 number 修饰符
> **input v-model.number="age" type="number"**
> .trim：如果要自动过滤用户输入的首尾空白字符，可以给 v-model 添加 trim 修饰符
> **input v-model.trim="msg"**

## 11、组件 ##
1、自定义组件是，每个组件必须只有一个根元素。
2、自定义事件：
1）子自定义组件中注册事件，方式如下：

    <button v-on:click="$emit('enlarge-text')">
      Enlarge text
    </button>
2）父组件或者根组件中根据事件名，监听子组件的事件：

    <blog-post
      ...
      v-on:enlarge-text="postFontSize += 0.1"
	或者 v-on:enlarge-text="onEnlargeText"为监听的事件添加参数
    ></blog-post>

	（处理带参数的事件）
	methods: {
	  onEnlargeText: function (enlargeAmount) {
	    this.postFontSize += enlargeAmount
	  }
	}

3、组件上使用 v-model
链接：https://cn.vuejs.org/v2/guide/components.html#%E5%9C%A8%E7%BB%84%E4%BB%B6%E4%B8%8A%E4%BD%BF%E7%94%A8-v-model

4、插槽

5、动态组件

	<component v-bind:is="currentTabComponent"></component>

## 11、自定义组件 ##
1、命名： kebab-case小写，更通用，因为在直接在 DOM (即非字符串的模板) 中使用时只有 kebab-case 是有效的。虽然普通情况下使用 PascalCase首字母大写也是有效的。
2、组件注册：
> 全局注册
> 局部注册



	var ComponentA = { /* ... */ }
	var ComponentB = { /* ... */ }
	var ComponentC = { /* ... */ }

	new Vue({
	  el: '#app',
	  components: {
	    'component-a': ComponentA,
	    'component-b': ComponentB
	  }
	})

对于 components 对象中的每个属性来说，其属性名就是自定义元素的名字，其属性值就是这个组件的选项对象。**注意局部注册的组件在其子组件中不可用。**

**12、模块系统**
> 1、在模块系统中局部注册
> 2、基础组件的自动化全局注册，实例：https://cn.vuejs.org/v2/guide/components-registration.html
> 属性验证：注意那些 prop 会在一个组件实例创建之前进行验证，所以实例的属性 (如 data、computed 等) 在 default 或 validator 函数中是不可用的。


13、$attrs 属性？？？

## 14、自定义事件 ##
1、事件命名规范：始终使用 kebab-case 的事件名
2、自定义组件的 v-model
> 一个组件上的 v-model 默认会利用名为 value 的 prop 和名为 input 的事件，但是像单选框、复选框等类型的输入控件可能会将 value 特性用于不同的目的
3、.sync 修饰符

## 15、插槽 ##
1、父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。
2、后备内容：类似于插槽默认值

## 16、自定义指令 ##
1、注册一个全局指令 v-focus

	<div id="app">
	    <p>页面载入时，input 元素自动获取焦点：</p>
	    <input v-focus>
	</div>
	 
	<script>
	// 注册一个全局自定义指令 v-focus
	Vue.directive('focus', {
	  // 当绑定元素插入到 DOM 中。
	  inserted: function (el) {
	    // 聚焦元素
	    el.focus()
	  }
	})
	// 创建根实例
	new Vue({
	  el: '#app'
	})
	</script>

2、directives 选项来注册局部指令v-focus

	<div id="app">
	  <p>页面载入时，input 元素自动获取焦点：</p>
	  <input v-focus>
	</div>
	 
	<script>
	// 创建根实例
	new Vue({
	  el: '#app',
	  directives: {
	    // 注册一个局部的自定义指令 v-focus
	    focus: {
	      // 指令的定义
	      inserted: function (el) {
	        // 聚焦元素
	        el.focus()
	      }
	    }
	  }
	})
	</script>

## 17、混入mixins ----定义了一部分可复用的方法或者计算属性##
1、全局混入（会影响每个单独创建的Vue实例）、局部混入。

## 18、 ES6笔记 ##
	const target = { a: 1 };
	const source1 = { b: 2 };
	const source2 = { c: 3 };
	Object.assign(target, source1, source2);
	target // {a:1, b:2, c:3}


1、assign合并的对象target只能合并source1、source2中的自身属性，并不会合并source1、source2中的继承属性，也不会合并不可枚举的属性，且无法正确复制get和set属性（会直接执行get/set函数，取return的值）。

2、有所部署了载了Iterator接口的对象(可遍历对象)都可以通过for...of去遍历，而for..in仅仅可以遍历对象