# Vue3学习笔记

## 页面启动逻辑

1.首先在index.html中使用div设置Vue应用空间

![img](https://img2023.cnblogs.com/blog/1570874/202303/1570874-20230305143001479-1338019542.png)

2.在main.js中的引用Vue方法，并创建Vue应用实例挂载到id为app的html标签（#对应id），main.css中存放的是一些样式

![img](https://img2023.cnblogs.com/blog/1570874/202303/1570874-20230305143020999-944175017.png)

3.App.vue文件中引用组件空间，并且可以直接通过引用的名称来直接使用组件，组件中有一些属性也可以直接指定（如下图中的msg）

![img](https://img2023.cnblogs.com/blog/1570874/202303/1570874-20230305143039215-296114900.png)

## vue应用的使用

1.创建应用并挂载到页面中。.mount这个方法一定要在整个应用配置和资源注册完之后再调用，其返回值是根组件实例而不是应用实例。页面可以支持多个应用挂载

```javascript
import { createApp } from 'vue'  // 1、引用vue方法
import App from './App.vue'  // 从一个单文件组件中导入根组件

const app = createApp(App);  // 2、添加根组件
/* 
或者：
const app = createApp({
  //根组件选项
});
*/

app.mount('#app') // 3、挂载应用；app为html的标签，如： <div id="app"></div>
```

2.应用配置

1.注册全局组件。必须再被挂载前完成。总共有两种方式：第一种是

```javascript
import { createApp } from 'vue'

  const app = createApp({})

  app.component(
    // 组件1-注册的名字
    'MyComponent',
    // 组件的实现
    {
      /* ... */
    }
    .component(
    // 组件2-注册的名字
    'MyComponent',
    // 组件的实现
    {
      /* ... */
    }
    .component(
    // 组件3-注册的名字
    'MyComponent',
    // 组件的实现
    {
      /* ... */
    }
  )
```

第二种是：使用单文件组件

```
import MyComponent from './App.vue'

  app
    .component('ComponentA', ComponentA)
    .component('ComponentB', ComponentB)
    .component('ComponentC', ComponentC)


...使用
<!-- 这在当前应用的任意组件中都可用；三个组件彼此独立 -->
<ComponentA/>
<ComponentB/>
<ComponentC/>
```

补充：全局变量和局部变量的区别

（1）全局注册的话，如果这个组件没有被使用，生产打包时同样会留着，不会自动消除

（2） 全局注册容易使得项目依赖关系变得模糊，影响维护，最好还是别用了

2.捕获全局异常

```javascript
app.config.errorHandler = (err) => { 

       /* 处理错误的一些具体操作 */ 

    }
```

## vue模板

1.vue模板能够声明式地将Vue组件实例绑定到网页DOM上。

2.vue会将模板编译为高度优化的JavaScript代码

3.使用DOM内嵌模板时，避免使用大写字母

## vue语法

### 数据类型

基础六种数据类型：Number(数字)、String(字符串)、Boolean(布尔)、Date(日期)、Array(数组)、Object(基础对象)

其他两种类型：Function(方法)、Symbol(属性)

自定义类型：类

### 控件数据绑定

#### 文本插值

1.最基础的数据绑定形式：文本插值，解析为文本。使用“mustache”语法（双大括号）

```html
    <span>Message: {{ msg }}</span>  // 使用双括号把js中的变量括起来，msg会随js中的变量变化而变化
```

2.解析为html字符串.v-html不能用来拼接组合模板

```html
    <p>Using text interpolation: {{ rawHtml }}</p>  // 解析为文本；Using text interpolation: <span style="color: red">This should be red.</span>
    <p>Using v-html directive: <span v-html="rawHtml"></span></p>  // 解析为Html字符串；Using v-html directive: This should be red.
```

#### 控件属性赋值

1.v-bind指令，可以直接用:代替。如果绑定的是null或者undefined，就会把这个属性移除。

```javascript
    <div v-bind:id="dynamicId"></div>
    <div :id="dynamicId"></div>
```

#### 控件绑定数据

动态绑定多个值，通过不带参数的v-bind进行绑定(没有了上面的‘id’)

```javascript
    <div v-bind="objectOfAttrs"></div>
    
    ...
    data() {
      return {
        objectOfAttrs: {
          id: 'container',
          class: 'wrapper'
        }
      }
    }
```

#### 使用JavaScript表达式

```javascript
    {{ number + 1 }}
    
    {{ ok ? 'YES' : 'NO' }}
    
    {{ message.split('').reverse().join('') }}
    
    <div :id="`list-${id}`"></div>
    
    <!-- 这是一个语句，而非表达式 -->
    {{ var a = 1 }}
    
    <!-- 条件控制也不支持，请使用三元表达式 -->
    {{ if (ok) { return message } }}
```

#### 调用函数

一般用@click给绑定到按钮上的

```javascript
    <span :title="toTitleDate(date)">
      {{ formatDate(date) }}
    </span>

    <el-button type="primary" @click="onSubmit">Create</el-button>
```

受限的全局访问：

模板中的表达式将被沙盒化，仅能够访问到[有限的全局对象列表](https://github.com/vuejs/core/blob/main/packages/shared/src/globalsWhitelist.ts#L3)。该列表中会暴露常用的内置全局对象，比如 `Math` 和 `Date`。

没有显式包含在列表中的全局对象将不能在模板内表达式中访问，例如用户附加在 `window` 上的属性。然而，你也可以自行在 [`app.config.globalProperties`](https://cn.vuejs.org/api/application.html#app-config-globalproperties) 上显式地添加它们，供所有的 Vue 表达式使用

#### 绑定响应式事件

计算属性，是一种特殊的方法

1.计算属性放在computed事件中，代替methods中的方法不可响应式更新到标签中。

```javascript
<p>Has published books:</p>
<span>{{ publishedBooksMessage }}</span>

export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
  computed: {
    // 一个计算属性的 getter
    publishedBooksMessage() {
      // `this` 指向当前组件实例
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}
```

2.计算属性默认是只读的。你可以通过同时提供 getter 和 setter 来创建“**可写计算属性**”。setter的方法可以更好的封装修改的功能

```javascript
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: {
      // getter
      get() {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set(newValue) {
        // 注意：我们这里使用的是解构赋值语法
        [this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

### 指令directives

1.v-if、v-else-if、v-else条件渲染

```javascript
<!-- v-if示例 -->
<p v-if="seen">Now you see me</p>

<!-- v-if、v-else-if、v-else示例 -->
<div v-if="Math.random() > 0.8">Now you see me</div>
<div v-else-if="Math.random() > 0.5">Now you see me</div>
<div v-else>Now you don't</div>

<!-- 切换不止一个元素进行“条件渲染”时的方案：使用<template>进行包裹；v-show 不支持在 <template> 元素上使用-->
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

2.v-show元素可见性

```javascript
    <h1 v-show="ok">Hello!</h1>
```

3.v-text更新元素的文本内容

```javascript
    <!-- v-text 通过设置元素的 textContent 属性来工作，因此它将覆盖元素中所有现有的内容。 -->
    <span v-text="msg"></span>
    <!-- 等同于 -->
    <span>{{msg}}</span>
```

4.v-html更新元素内嵌HTML，直接输出html编译的内容

```javascript
    <div v-html="html"></div>
```

5.v-pre跳过该元素极其子元素的编译，原封不动输出内容

```javascript
    <span v-pre>{{ this will not be compiled }}</span>
```

6.v-bind数据绑定

```javascript
    <a v-bind:href="url"> ... </a>

    <!-- 简写为: -->
    <a :href="url"> ... </a>

    <!--
    动态参数绑定
    举例：如果你的组件实例有一个数据属性 attributeName，其值为 "href"，那么这个绑定就等价于 v-bind:href
    注意，参数表达式有一些约束，
    参见下面“动态参数值的限制”与“动态参数语法的限制”章节的解释
    -->
    <a v-bind:[attributeName]="url"> ... </a>
    <a :[attributeName]="url"> ... </a>
```

7.v-model数据双向绑定

7.1 简单示例

```JavaScript
    <!-- 示例一：页面元素或表单数据的绑定Start -->
    <!-- 不使用v-model时 -->
    <input
      :value="text"
      @input="event => text = event.target.value">
    <!-- 使用v-model时 -->
    <input v-model="text">
    <!-- 示例一：页面元素或表单数据的绑定End -->


    <!-- 示例二：组件使用v-model_Start -->
    <!--①将内部原生 <input> 元素的 value attribute 绑定到 modelValue prop
        ②当原生的 input 事件触发时，触发一个携带了新值的 update:modelValue 自定义事件 -->
    <!-- CustomInput.vue -->
	<script>
	export default {
	  props: ['modelValue'],
	  emits: ['update:modelValue']
	}
	</script>
	<template>
	  <input
	    :value="modelValue"
	    @input="$emit('update:modelValue', $event.target.value)"
	  />
	</template>
    <!-- 组件使用的地方 -->
    <CustomInput v-model="searchText" />
    <!-- 示例二：组件使用v-model_End -->

        
        
        //多行文本是哟个v-model，要结合placeholder=“add multiple lines"
<template>
  <span>Multiline message is:</span>
  <p style="white-space: pre-line;">{{ message }}</p>
  <textarea v-model="message" placeholder="add multiple lines"></textarea>
</template>

<script>
export default {
  data() {
    return {
      message: ''
    }
  }
}
</script>
```

7.2 复选框使用v-model：一般使用bool值，但是也可以赋值为数组，复选框打勾的结果作为数组的一个元素；复选框的值绑定使用true-value和false-value这两个，这两个仅仅支持和v-model配套使用

```JavaScript
<template>
  <!-- 普通用法 v-model=布尔值 -->
  <input type="checkbox" id="checkbox" v-model="checked" />
  <label for="checkbox">{{ checked }}</label> 
    
  <!-- 值绑定用法1：true-value="yes"、false-value="no"；这里 toggle 属性的值会在选中时被设为 'yes'，取消选择时设为 'no' -->
  <input type="checkbox" v-model="toggle"
  true-value="yes" false-value="no" />

  <!-- 值绑定用法2：给true-value、false-value绑定js数据 -->
  <input type="checkbox" v-model="toggle"
  :true-value="dynamicTrueValue" :false-value="dynamicFalseValue" />

  <!-- 特殊用法 v-model=数组 -->
  <div>Checked names: {{ checkedNames }}</div>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames" />
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
  <label for="mike">Mike</label>
</template>

<script>
export default {
  data() {
    return {
      checked: true,
      checkedNames: []
    }
  }
}
</script>
```

7.3 单选按钮使用v-model：复选框值绑定要使用：value

```JavaScript
<template>
  <!-- 普通示例 -->
  <div>Picked: {{ picked }}</div>
  <input type="radio" id="one" value="One" v-model="picked" />
  <label for="one">One</label>
  <input type="radio" id="two" value="Two" v-model="picked" />
  <label for="two">Two</label>
    
  <!-- 值绑定示例；值绑定使用 v-model+:value；pick 会在第一个按钮选中时被设为 first，在第二个按钮选中时被设为 second -->
  <input type="radio" v-model="pick" :value="first" />
  <input type="radio" v-model="pick" :value="second" />
</template>

<script>
export default {
  data() {
    return {
      picked: 'One'
    }
  }
}
</script>
```

7.4 选择器使用v-model

```JavaScript
<template>
  <!-- 单选 -->
  <span> Selected: {{ selected }}</span>
  <select v-model="selected">
    <option disabled value="">Please select one</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
    
  <!-- 多选 -->
  <div>Selected: {{ selected }}</div>
  <select v-model="selected" multiple>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
    
  <!-- 值绑定示例；当某个选项被选中，selected 会被设为该对象字面量值 { number: 123 }。 -->
  <select v-model="selected">
    <!-- 内联对象字面量 -->
    <option :value="{ number: 123 }">123</option>
  </select>
</template>

<script>
export default {
  data() {
    return {
      selected: '',
      selected: []
    }
  }
}
</script>
```

7.5 组件上使用v-model

```JavaScript
  <CustomInput v-model="searchText" />
  //组内处理方式1
  <!-- CustomInput.vue -->
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  computed: {
    value: {
      get() {
        return this.modelValue
      },
      set(value) {
        this.$emit('update:modelValue', value)
      }
    }
  }
}
</script>

<template>
  <input v-model="value" />
</template>
//组内处理方式2
<!-- CustomInput.vue -->
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue']
}
</script>

<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>

//如果有多个props和emits，只需要多绑定几个就行
<UserName v-model:first-name="first" v-model:last-name="last"/>//这边写了first-name，对应的就是下面那个vue文件中的firstName

<!-- UserName.vue -->
<template>
  <input
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)"
  />
  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)"
  />
</template>
<script>
export default {
  props: {
    firstName: String,
    lastName: String
  },
  emits: ['update:firstName', 'update:lastName']
}
</script>
```

7.6 加了.lazy会使得数据更新发生在change事件后，而不是input事件后；.number会使输入子弟哦那个变成数字；.trim默认去除输入内容中的两端的空格

7.7 自定义修饰符

```JavaScript
<MyComponent v-model.capitalize="myText" />

<!-- MyComponent -->
<template>
  <!-- 定义:value与@input事件 -->
  <input type="text" :value="modelValue" @input="emitValue" />
</template>

<script>
export default {
  props: {
    modelValue: String,  // 
    modelModifiers: {   // 修饰符组
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  methods: {
    emitValue(e) {  // input事件中写我们自定义的修饰符的处理逻辑
      let value = e.target.value
      if (this.modelModifiers.capitalize) {  // 使用了自定义的capitalize修饰符时，如：<MyComponent v-model.capitalize="myText" />
        value = value.charAt(0).toUpperCase() + value.slice(1)  // 处理逻辑
      }
      this.$emit('update:modelValue', value)
    }
  }
}
</script>
```

### 数据循环赋值v-for

v-if比v-for优先级高

1.简单示例

```JavaScript
<!-- v-for示例一；循环10次；注：此处 n 的初值是从 1 开始而非 0。 -->
<span v-for="n in 10">{{ n }}</span>

<!-- v-for示例二；注：v-for="item of items"也是可以的 -->
<div v-for="item in items">{{ item.text }}</div>

<!-- v-for可访问父类对象 -->
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
<!-- 结果：
    Parent - 0 - Foo
    Parent - 1 - Bar
-->

<!-- v-for第二个参数默认情况下表示当前项的位置索引 -->
<div v-for="(value, index) in object">内容：{{ value}},位置：{{ index }}</div>
<!-- v-for也可以自定义第二个参数标识属性名，第三个参数表示位置索引 -->
<div v-for="(value, name, index) in object">内容：{{value}},名称{{ name }},位置：{{ index }}</div>

...
data() {
  return {
    parentMessage: 'Parent',
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

2.v-for+key：`v-for`的默认方式是尝试就地更新元素而不移动它们。要强制其重新排序元素，你需要用特殊 attribute `key` 来提供一个排序提示

```javascript
    <div v-for="item in items" :key="item.id">{{ item.text }}</div>
```

3.template上使用v-for

```javascript
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

4.自定义组件上用v-for

```JavaScript
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```

5.v-for创建的数组的变更方法：push，pop，shift，unshift，splice，sort，reverse

### v-on监听DOM事件

1.内联事件处理器

```JavaScript
<!-- 示例一：简单示例 -->
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>

<!-- 示例二：使用特殊的 $event 变量 -->
<button @click="warn('Form cannot be submitted yet.', $event)">//这个event是vue的一个原生事件对象，可以把整个事件对象传递给warn方法
  Submit
</button>

<!-- 示例三：使用内联箭头函数 -->
<button @click="(event) => warn('Form cannot be submitted yet.', event)">
  Submit
</button>

data() {
  return {
    count: 0
  }
}

methods: {
  warn(message, event) {
    // 这里可以访问 DOM 原生事件
    if (event) {
      event.preventDefault()//这个event有一些方法可以调用
    }
    alert(message)
  }
}
```

2.方法事件处理器

```JavaScript
<!-- 示例一：无参写法;`greet` 是定义过的方法名 -->
<button @click="greet">Greet</button>
<!-- 示例一：有参写法 -->
<button @click="say('hello')">Say hello</button>

data() {
  return {
    name: 'Vue.js'
  }
},
methods: {
    greet(event) {  // 没有类型标注时，这个 event 参数会隐式地标注为 any 类型;显式转化写法为 greet(event: Event) { }
    // 方法中的 `this` 指向当前活跃的组件实例
    alert(`Hello ${this.name}!`)
    // `event` 是 DOM 原生事件
    if (event) {
      alert(event.target.tagName)
    },
    say(message) {
      alert(message)
    }
  }
}
```

3.事件修饰符

event有很多方法，比如：.stop;.prevent;.self;.capture;.once;.passive

```javascript
<!-- 单击事件将停止传递 -->
<a @click.stop="doThis"></a>

<!-- 提交事件将不再重新加载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰语可以使用链式书写 -->
<a @click.stop.prevent="doThat"></a>

<!-- 也可以只有修饰符 -->
<form @submit.prevent></form>

<!-- 仅当 event.target 是元素本身时才会触发事件处理器 -->
<!-- 例如：事件处理器不来自子元素 -->
<div @click.self="doThat">...</div>

<!-- 添加事件监听器时，使用 `capture` 捕获模式 -->
<!-- 例如：指向内部元素的事件，在被内部元素处理前，先被外部处理 -->
<div @click.capture="doThis">...</div>

<!-- 点击事件最多被触发一次 -->
<a @click.once="doThis"></a>

<!-- 滚动事件的默认行为 (scrolling) 将立即发生而非等待 `onScroll` 完成 -->
<!-- 以防其中包含 `event.preventDefault()` -->
<div @scroll.passive="onScroll">...</div>
```

4.按键修饰符

@keyup，也可以直接使用暴露的按键名称作为修饰符

```JavaScript
<!-- 仅在 `key` 为 `Enter` 时调用 `submit` -->
<input @keyup.enter="submit" />//这里就指定了是“enter”这个键
```

常用的有：enter,tab,delete,esc,space,up,down,left,right,ctrl,alt,shift,meta

### v-slot组件中的占位属性

用于声明具名插槽或是期望接受props的作用域插槽

基本示例

```JavaScript
<FancyButton>
  Click me! <!-- 插槽内容 -->
</FancyButton>

//插槽的内容可以是任意合法的模板内容，不局限于文本
<FancyButton>
  <span style="color:red">Click me!</span>
  <AwesomeIcon name="plus" />
</FancyButton>


//FancyButton模板
<button type="submit">
  <slot></slot>
</button>
//插槽的内容可以是任意合法的模板内容，不局限于文本
<FancyButton>
  <span style="color:red">Click me!</span>
  <AwesomeIcon name="plus" />
</FancyButton>
```

如上，Click me就会插入到slot指示的这个位置

1.插槽中的默认内容。如果父组件没有提供任何插槽内容，那么插槽就可以使用自己的默认内容

```JavaScript
<button type="submit">
  <slot>
    Submit <!-- 默认内容 -->
  </slot>
</button>
```

2.具名插槽。相当于给slot一个name，这样就可以指定插入位置，不给name就隐式命名为default，要给具名插槽传内容，就需要v-slot来指定

```JavaScript
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>

<BaseLayout>
  <template v-slot:header>
    <!-- header 插槽的内容放这里 -->
  </template>
//也可以写成这样的
<template #header>
    <h1>Here might be a page title</h1>
  </template>
</BaseLayout>
```

3.条件插槽。当 header、footer 或 default 的内容存在时，我们希望包装它以提供额外的样式

```javascript
<template>
  <div class="card">
    <div v-if="$slots.header" class="card-header">
      <slot name="header" />
    </div>
    
    <div v-if="$slots.default" class="card-content">
      <slot />
    </div>
    
    <div v-if="$slots.footer" class="card-footer">
      <slot name="footer" />
    </div>
  </div>
</template>
```

4.作用域插槽。同时使用父子组件中的值，那么给插槽的出口传递属性值

```JavaScript
//默认插槽如何接收props，如下，父组件就获得了子组件的两个值
<!-- <MyComponent> 的模板 -->
<div>
  <slot :text="greetingMessage" :count="1"></slot>
</div>
<MyComponent v-slot="slotProps">
  {{ slotProps.text }} {{ slotProps.count }}
</MyComponent>

//也可以写作
<MyComponent v-slot="{ text, count }">
  {{ text }} {{ count }}
</MyComponent>
```

5.具名作用域插槽，允许子向父传

```JavaScript
<MyComponent>
  <template #header="headerProps">//#后面指定的就是插槽的name，再后面这个headerProps是用来接收返回值的对象，可以通过headerProps.xxx来获取一些子组件的属性
    {{ headerProps }}
  </template>

  <template #default="defaultProps">
    {{ defaultProps }}
  </template>

  <template #footer="footerProps">
    {{ footerProps }}
  </template>
</MyComponent>

<slot name="header" message="hello"></slot>
```

注意，同时使用了具名插槽与默认插槽，则需要为默认插槽使用显式的 `<template>` 标签

## Vue组件使用

书写风格分为选项式api和组合式api。我们用选项式风格来写，选项式就是比如data，mounted，methods这些都是选项

### 组件基础

当使用构建步骤时，我们一般会将 Vue 组件定义在一个单独的 `.vue` 文件中，这被叫做[单文件组件](https://cn.vuejs.org/guide/scaling-up/sfc.html) (简称 SFC)。

组件名官方推荐使用PascalCase命名规则，如：`<PascalCase />`；但是，PascalCase 的标签名在 DOM 模板中是不可用的。为了方便，Vue 支持将模板中使用 kebab-case 的标签解析为使用 PascalCase 注册的组件。这意味着一个以 `MyComponent` 为名注册的组件，在模板中可以通过 `<MyComponent>` 或 `<my-component>` 引用

1.定义一个组件并使用。先在要用这个组件的文件中加入这个组件，然后在export default里面注册一下这个component就可以了。

```javascript
<script>
import ButtonCounter from './Test.vue'

export default {
  components: {
    ButtonCounter
  }
}
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

2.声明响应式变量的区域  data

选用选项式api，用data选项来声明组件的响应式状态

```JavaScript
export default {
  data() {
    return {
      count: 1,
      someObject: {}
    }
  },

  // `mounted` 是生命周期钩子，之后我们会讲到
  mounted() {
    // `this` 指向当前组件实例
    console.log(this.count) // => 1

    // 数据属性也可以被更改
    this.count = 2
    
    const newObject = {}  // 不属于响应式变量
    this.someObject = newObject
    console.log(newObject === this.someObject) // false
  }
}
```

注意：Vue 在组件实例上暴露的内置 API （vue自己事先定义好的一些内置方法，比如emit这些）使用 `$` 作为前缀。它同时也为内部属性保留 `_` 前缀。因此，你应该避免在顶层 `data` 上使用任何以这些字符作前缀的属性，也就是不要在data里面定义$和_开头的属性

3.声明方法的区域  methods

```JavaScript
<button @click="increment1">{{ count }}</button>

export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment1() {      // 方法风格1：方法绑定了永远指向组件实例的 this。
      this.count++
    },
    increment2: () => {  // 方法风格2：表达式风格；箭头函数没有自己的 this 上下文。
      // 反例：无法访问此处的 `this`!
    }
  },
  mounted() {
    this.increment1();  // 正常使用，this中可以找到increment1函数；
    this.increment2();  // 报错，this中找不到increment2函数。
  }
}
```

注：如上面的示例，Vue 自动为 `methods` 中的方法绑定了永远指向组件实例的 `this`。这确保了方法在作为事件监听器或回调函数时始终保持正确的 `this`。你不应该在定义 `methods` 时使用箭头函数，因为箭头函数没有自己的 `this` 上下文

4.组件间传值  props和$emit、events

props：接收父组件的值（谁用了这个组件谁就是它的父类）。可以是任何类型的值，设置默认值使用props，required设置传递约束，也可以不设置，校验使用validator

```JavaScript
export default {
  class Person {
    constructor(firstName, lastName) {
      this.firstName = firstName
      this.lastName = lastName
    }
  },

  props: {
    // 基础类型检查
    //（给出 `null` 和 `undefined` 值则会跳过任何类型检查）
    propA: Number,
    // 多种可能的类型
    propB: [String, Number],
    // 必传，且为 String 类型
    propC: {
      type: String,
      required: true
    },
    // Number 类型的默认值
    propD: {
      type: Number,
      default: 100
    },
    // 对象类型的默认值
    propE: {
      type: Object,
      // 对象或者数组应当用工厂函数返回。
      // 工厂函数会收到组件所接收的原始 props
      // 作为参数
      default(rawProps) {
        return { message: 'hello' }
      }
    },
    // 自定义类型校验函数
    propF: {
      validator(value) {
        // The value must match one of these strings
        return ['success', 'warning', 'danger'].includes(value)
      }
    },
    // 自定义类型(无校验函数、只校验是不是该类型的实例化类)
    propH: Person,
    // 函数类型的默认值
    propG: {
      type: Function,
      // 不像对象或数组的默认，这不是一个
      // 工厂函数。这会是一个用来作为默认值的函数
      default() {
        return 'Default function'
      }
    }
  }
}
```

使用实例：

```JavaScript
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title']
}
</script>

<template>
  <h4>{{ title }}</h4>
</template>
//父文件
<script>
import BlogPost from './BlogPost.vue'
  
export default {
  components: {
    BlogPost
  },
  data() {
    return {
      posts: [
        { id: 1, title: 'My journey with Vue' },
        { id: 2, title: 'Blogging with Vue' },
        { id: 3, title: 'Why Vue is so fun' }
      ]
    }
  }
}
</script>

<template>
    <!-- 示例1 -->
    <BlogPost title="My journey with Vue" />
    <!-- 示例2-->
	<BlogPost
  	v-for="post in posts"
	  :key="post.id"
  	:title="post.title"
	></BlogPost>
</template>
```

$emit：调用父组件的方法并传递数据。组件通过emit来触发父组件的事件，父组件通过v-on（简写为@）来监听子组件的触发。

示例：

```JavaScript
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title'],
  emits: ['enlarge-text']//指明了可能要触发的父组件中的事件的名称
}
</script>

<template>
  <div class="blog-post">
	  <h4>{{ title }}</h4>
	  <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
<!-- APP.vue -->
<script>
import BlogPost from './BlogPost.vue'
  
export default {
  components: {
    BlogPost
  },
  data() {
    return {
      posts: [
        { id: 1, title: 'My journey with Vue' },
        { id: 2, title: 'Blogging with Vue' },
        { id: 3, title: 'Why Vue is so fun' }
      ],
      postFontSize: 1
    }
  }
}
</script>

<template>
  <div :style="{ fontSize: postFontSize + 'em' }">
    <BlogPost
      v-for="post in posts"
      :key="post.id"
      :title="post.title"
      @enlarge-text="postFontSize += 0.1"//直接在这里定义了一个事件并实现了具体操作，而不是在method中实现
    ></BlogPost>
  </div>
</template>


//为emit添加参数
//MyBUtton.vue
<button @click="$emit('increaseBy', 1)">
  Increase by 1
</button>

//APP.vue
 <MyButton @increase-by="(n) => count += n" />//可见事件触发也支持书写格式的自动转变

//或者：
 <MyButton @increase-by="increaseCount" />//我认为这种方法更统一，更好
 ...
 methods: {
   increaseCount(n) {
     this.count += n
   }
 }

//添加校验，和props检验类型差不多，这边校验email和password的存在性
export default {
  emits: {
    // 没有校验
    click: null,

    // 校验 submit 事件
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm(email, password) {
      this.$emit('submit', { email, password })
    }
  }
}
```

events：用来声明需要抛出的方法或变量，emit发送的那个东西就叫事件

5.DOM更新机制

当你更改响应式状态后，DOM 会自动更新。然而，你得注意 DOM 的更新并不是同步的。相反，Vue 将缓冲它们直到更新周期的 “下个时机” 以确保无论你进行了多少次状态更改，每个组件都只更新一次。

若要等待一个状态改变后的 DOM 更新完成，你可以使用 [nextTick()](https://cn.vuejs.org/api/general.html#nexttick) 这个全局 API

```JavaScript
import { nextTick } from 'vue'

export default {
  methods: {
    increment() {
      this.count++
      nextTick(() => {
        // 访问更新后的 DOM
      })
    }
  }
}
```

6.理解千层作用形式。

在 Vue 中，状态都是默认深层响应式的。这意味着响应式变量的“子项”及“子项的子项”具有响应式。Vue也提供了创建“[浅层响应式对象](https://cn.vuejs.org/api/reactivity-advanced.html#shallowreactive)”的方法`shallowReactive()`，可使“变量子项的子项”不具备响应式

```JavaScript
const state = shallowReactive({
  foo: 1,
  nested: {
    bar: 2
  }
})

// 更改状态自身的属性是响应式的
state.foo++

// ...但下层嵌套对象不会被转为响应式
isReactive(state.nested) // false

// 不是响应式的
state.nested.bar++
```

7.阻止短期内响应事件重复触发1-Vue防抖_按钮最后一次响应再执行事件

用户可能存在疯狂点击同一个按钮的情况存在，可能导致表单重复提交啥的。

```JavaScript
export default {
  created() {
    // 每个实例都有了自己的预置防抖的处理函数
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // 最好是在组件卸载时
    // 清除掉防抖计时器
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... 对点击的响应 ...
    }
  }
}
```

### 组件样式绑定

1.绑定静态样式

```
<div class="static" /div>
```



2.动态绑定样式_组件内

```JavaScript
<!-- :class使用 -->
<div :class="{ active: isActive, 'text-danger': hasError }" /div>
<!-- 结果<div class="static active"></div> -->
    
<!-- :class支持数组 -->
<div :class="[activeClass, errorClass]"></div>
<!-- 结果<div class="active text-danger"></div> -->
    
<!-- :class支持数组内计算 -->
<div :class="[{ active: isActive }, errorClass]"></div>

<!-- :class支持三元表达式 -->
<div :class="[isActive ? activeClass : '', errorClass]"></div>

...
data() {
  return {
    isActive: true,
    hasError: false,
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}

//在js中创建样式
<div :class="classObject" /div>

data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}

//与计算属性一起使用
<div :class="classObject"></div>

data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

3.动态绑定样式_组件外

```javascript
<!-- 子组件模板；组件名MyComponent -->
<p class="foo bar">Hi!</p>

<!-- 在使用组件时 -->
<MyComponent :class="{ active: isActive }" />

<!-- 结果 -->
<p class="foo bar active">Hi!</p>
```



4.使用内联样式  style

```javascript
//驼峰写法
<div :style="{ color: 'red', fontSize: fontSize1 + 'px' }"></div>

data() {
  return {
    fontSize1: 30
  }
}
//css实际写法，注意单引号
<div :style="{ 'font-size': fontSize1 + 'px' }"></div>

data() {
  return {
    color: 'red',
    fontSize1: '13px'
  }
}
//绑定内联样式与内联样式数组
<!-- 绑定单个内联样式 -->
<div :style="styleObject"></div>

<!-- 绑定内联样式数组 -->
<div :style="[baseStyles, overridingStyles]"></div>


data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

style支持对一个样式属性提供多个值，以解决某个浏览器需要加浏览器特殊前缀才能正常渲染的问题

## 深入组件

### 模板引用 ref="input"

虽然 Vue 的声明性渲染模型为你抽象了大部分对 DOM 的直接操作，但在某些情况下，我们仍然需要直接访问底层 DOM 元素。要实现这一点，我们可以使用特殊的 `ref` attribute

① 创建引用标签：<input ref="名称" />

② 访问模板引用：this.$refs.名称

```javascript
<template>
  <input ref="input" />
</template>

<script>
export default {
  mounted() {
    this.$refs.input.focus()  // 访问模板引用，如：this.$refs.名称
  }
}
</script>
```

1.v-for中的模板引用：当在 `v-for` 中使用 模板引用 时，相应的引用中包含的值是一个数组，但是ref数组并不保证和源数组相应的顺序。ref是一个特殊属性，用于注册对DOM元素或组件实例的引用

```javascript
<template>
  <ul>
    <li v-for="item in list" ref="items">//这里使用了ref，所以会收集所有循环项的引用，所有收集到的都放到了items里面，也就是这边起的一个名字
      {{ item }}
    </li>
  </ul>
</template>

<script>
export default {
  data() {
    return {
      list: [1, 2, 3]
    }
  },
  mounted() {
    console.log(this.$refs.items)
  }
}
</script>
```

2.函数模板引用中使用ref。除了使用字符串值作名字，`ref` attribute 还可以绑定为一个函数，会在每次组件更新时都被调用，该函数会收到元素引用作为其第一个参数

```javascript
<input :ref="(el) => { /* 将 el 赋值给一个数据属性或 ref 变量 */ }">
```

注意我们这里需要使用动态的 `:ref` 绑定才能够传入一个函数。当绑定的元素被卸载时，函数也会被调用一次，此时的 `el` 参数会是 `null`。你当然也可以绑定一个组件方法而不是内联函数

3.组件上ref的基础使用

如果一个子组件使用的是选项式 API ，被引用的组件实例和该子组件的 `this` 完全一致，这意味着父组件对子组件的每一个属性和方法都有完全的访问权。这使得在父组件和子组件之间创建紧密耦合的实现细节变得很容易，当然也因此，应该只在绝对需要时才使用组件引用。大多数情况下，你**应该首先使用标准的 props 和 emit 接口来实现父子组件交互**

```java
<script>
import Child from './Child.vue'

export default {
  components: {
    Child
  },
  mounted() {
    // this.$refs.child 是 <Child /> 组件的实例
  }
}
</script>

<template>
  <Child ref="child" />
</template>
```

4.组件上的ref-限制父组件对子组件的访问

下面这个例子中，父组件通过模板引用访问到子组件实例后，仅能访问 `publicData` 和 `publicMethod`

```JavaScript
export default {
  expose: ['publicData', 'publicMethod'],
  data() {
    return {
      publicData: 'foo',
      privateData: 'bar'
    }
  },
  methods: {
    publicMethod() {
      /* ... */
    },
    privateMethod() {
      /* ... */
    }
  }
}
```

## 内置组件

1.KeepAlive-缓存容器组件

1）保留/排除 对某组件的缓存`include` / `exclude`

2）设置最大缓存实例数`max`

3）设置缓存实例的生命周期：当一个组件实例从 DOM 上移除但因为被 `<KeepAlive>` 缓存而仍作为组件树的一部分时，它将变为**不活跃**状态而不是被卸载。可以通过 [`activated`](https://cn.vuejs.org/api/options-lifecycle.html#activated) 和 [`deactivated`](https://cn.vuejs.org/api/options-lifecycle.html#deactivated) 选项来注册自身的释放。

```javascript
<!-- 以英文逗号分隔的字符串 -->
<KeepAlive include="a,b">
  <component :is="view" />
</KeepAlive>

<!-- 正则表达式 (需使用 `v-bind`) -->
<KeepAlive :include="/a|b/">
  <component :is="view" />
</KeepAlive>

<!-- 数组 (需使用 `v-bind`) -->
<KeepAlive :include="['a', 'b']">
  <component :is="view" />
</KeepAlive>

<KeepAlive :max="10">
  <component :is="activeComponent" />
</KeepAlive>

...
export default {
  activated() {
    // 在首次挂载、
    // 以及每次从缓存中被重新插入的时候调用
  },
  deactivated() {
    // 在从 DOM 上移除、进入缓存
    // 以及组件卸载时调用
  }
}
```

## 生命周期

![img](https://img2023.cnblogs.com/blog/1570874/202304/1570874-20230426213242355-1130123300.png)

选项式钩子的示例

```JavaScript
<!-- CustomInput.vue -->
<template>
  <input v-model="value" />
</template>

<script>
export default {
  // 一、组件实例化阶段的生命周期函数：
  beforeCreate(){  // 1、组件实例初始化完成、props 解析之后;data()、computed之前
  },
  computed(): {      // 2、响应式数据、计算属性、方法和侦听器设置
    // 常用
  },
  created(){       // 3、在组件实例处理完所有与状态相关的选项后；即：响应式数据、计算属性、方法和侦听器设置完成后，挂载前。
  },
  beforeMount(){   // 4、组件被挂载前；这个钩子在服务端渲染时不会被调用。
  },
  mounted(){       // 5、组件被挂载后；这个钩子在服务端渲染时不会被调用。
    // 常用
  },

  // 二、组件更新阶段的生命周期函数
  beforeUpdate(){  // 1、组件更新之前；这个钩子在服务端渲染时不会被调用。
  },
  updated(){       // 2、组件更新之后；父组件的更新钩子将在其子组件的更新钩子之后调用。这个钩子在服务端渲染时不会被调用。
    // 常用
    // 注：这个钩子会在组件的任意 DOM 更新后被调用，这些更新可能是由不同的状态变更导致的。
    // 如果你需要在某个特定的状态更改后访问更新后的 DOM，请使用 nextTick() 作为替代。
  },
  activated(){     // 3、若组件实例是 <KeepAlive> 缓存树的一部分，当组件被插入到 DOM 中时调用；这个钩子在服务端渲染时不会被调用。
    
  },
  deactivated(){   // 4、若组件实例是 <KeepAlive> 缓存树的一部分，当组件从 DOM 中被移除时调用；这个钩子在服务端渲染时不会被调用。
    
  },

  // 三、组件卸载阶段的生命周期函数
  beforeUnmount(){  // 1、组件卸载之前；这个钩子在服务端渲染时不会被调用。
  },
  unmounted(){      // 2、组件卸载之后；这个钩子在服务端渲染时不会被调用。
    // 常用
    // 可以在这个钩子中手动清理一些副作用，例如计时器、DOM 事件监听器或者与服务器的连接。
  },

  // 四、其他钩子
  errorCaptured(){   // 1、在捕获了后代组件传递的错误时调用。
    // 可以获取到的组件：组件渲染、事件处理器、生命周期钩子、setup() 函数、侦听器、自定义指令钩子、过渡钩子
    // 这个钩子带有三个实参：错误对象、触发该错误的组件实例、以及一个说明错误来源类型的信息字符串。
    
    // 错误传递规则：
    // 默认情况下，所有的错误都会被发送到应用级的 app.config.errorHandler (前提是这个函数已经定义)，这样这些错误都能在一个统一的地方报告给分析服务。
    // 如果组件的继承链或组件链上存在多个 errorCaptured 钩子，对于同一个错误，这些钩子会被按从底至上的顺序一一调用。这个过程被称为“向上传递”，类似于原生 DOM 事件的冒泡机制。
    // 如果 errorCaptured 钩子本身抛出了一个错误，那么这个错误和原来捕获到的错误都将被发送到 app.config.errorHandler。
    // errorCaptured 钩子可以通过返回 false 来阻止错误继续向上传递。即表示“这个错误已经被处理了，应当被忽略”，它将阻止其他的 errorCaptured 钩子或 app.config.errorHandler 因这个错误而被调用。
  },
  serverPrefetch(){  // 2、当组件实例在服务器上被渲染之前要完成的异步函数。
    // 如果这个钩子返回了一个 Promise，服务端渲染会在渲染该组件前等待该 Promise 完成。
  },
  renderTracked (){    // 3、在一个响应式依赖被组件的渲染作用追踪后调用。
    // 这个钩子仅在开发模式下可用，且在服务器端渲染期间不会被调用。
  },
  renderTriggered (){  // 4、在一个响应式依赖被组件触发了重新渲染之后调用。
    // 这个钩子仅在开发模式下可用，且在服务器端渲染期间不会被调用。
  }
}
</script>
```

## 倾听器watch

计算属性允许我们声明性地计算衍生值。然而在有些情况下，我们需要在状态变化时执行一些“副作用”

1.简单示例：

```javascript
<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</template>

<script>
export default {
  data() {
    return {
      question: '',
      answer: 'Questions usually contain a question mark. ;-)'
    }
  },
  watch: {
    // whenever question changes, this function will run
    question(newQuestion, oldQuestion) {
      if (newQuestion.indexOf('?') > -1) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      this.answer = 'Thinking...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer
      } catch (error) {
        this.answer = 'Error! Could not reach the API. ' + error
      }
    }
  }
}
</script>
```

1)命令式创建侦听器

```javascript
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

2)改变侦听器的响应时刻

默认情况下，用户创建的侦听器回调，都会在 Vue 组件更新**之前**被调用。这意味着你在侦听器回调中访问的 DOM 将是被 Vue 更新之前的状态。**如果想在侦听器回调中能访问被 Vue 更新之后的 DOM，你需要指明 `flush: 'post'`** 选项：

```javascript
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'post'
    }
  }
}
```

2.深层侦听器用法

`watch` 默认是浅层的：被侦听的属性，仅在被赋新值时，才会触发回调函数——而嵌套属性的变化不会触发。如果想侦听所有嵌套的变更，你需要深层侦听器，但是开销很大，尽量不用

```javascript
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // 注意：在嵌套的变更中，
        // 只要没有替换对象本身，
        // 那么这里的 `newValue` 和 `oldValue` 相同
      },
      deep: true  // 声明为深层
    }
  }
}
```

3.即时回调的侦听器

`watch` 默认是懒执行的：仅当数据源变化时，才会执行回调。但在某些场景中，我们希望在创建侦听器时，立即执行一遍回调。举例来说，我们想请求一些初始数据，然后在相关状态更改时重新请求数据

```JavaScript
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // 在组件实例创建时会立即调用
      },
      // 强制立即执行回调
      immediate: true
    }
  }
  // ...
}
```

