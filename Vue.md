## 基础

### 创建项目

因为非官方，不像AS这样配套IDE，所以需要 vue-cli 这样的命令行工具协助自动创建项目脚手架（基础结构）

```bash
vue create [projectName]

或

vue ui
```



### 打包项目

最后还是要到js、html、css原始格式，所以需要vue「翻译」最终结果，所以要用wetpack或vite等工具

* wetpack 是市面主流，类似于AIO模式，全部打包

* vite 是vue官方出的，更轻量，类似JIT模式，按需编译打包。
  也可以用于创建项目，对标vue-cli，同样类JIT模式（依赖包先不安装，运行的时候再按需加载）





### 修饰符 Modifiers

修饰符是以点开头的特殊后缀，表明指令需要以一些特殊的方式被绑定。例如 `.prevent` 修饰符会告知 `v-on` 指令对触发的事件调用 `event.preventDefault()` （屏蔽了原有默认行为，如链接点击打开网页）

```
<form @submit.prevent="onSubmit">...</form>
```



### 计算属性

因为是响应式，所以才有计算属性

如果命令式，一般是复杂计算，最后绑定数据和UI

但响应式，数据到UI前的处理怎么办呢？用计算属性



和方法的区别

方法会在重新渲染时每次都调用，而计算属性是有缓存的，只会在依赖属性变化时才会调用getter函数



也可以修改



### 属性监听

针对data属性变化的监听

```vue
  watch: {
    // 每当 question 改变时，这个函数就会执行
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  }
```





### Class Style

和其他属性类似，class 和 style 都可以用 v-bind:class / :class 和 :style 的方式注入



> 问题：
>
> 这样用 v-bind: 的方式和直接 css方式哪个更常用？





### v-for

:key 的影响



### v-model

默认的响应式是单向绑定的，数据->视图

对于表单类组件等可变的（输入框等），需要双向绑定，视图里修改后也可以回改视图

```vue
<input v-model="searchText" />

<!-- 等同于 -->
<input
  :value="searchText"
  @input="searchText = $event.target.value"
/>
```



v-model会被分别编译成 `:value`（数据->视图）以及 `@input` 监听（视图->数据）从而实现双向绑定





### ref

相当于引用

```vue
<script>
export default {
  mounted() {
    this.$refs.input.focus()
  }
}
</script>

<template>
  <input ref="input" />
</template>
```



## 自定义组件

### 值传递

父子组件之间属性传递，通过 `props`

```vue
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title']
}
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

```vue
<BlogPost title="My journey with Vue" />
<BlogPost title="Blogging with Vue" />
<BlogPost title="Why Vue is so fun" />
```

props的值只能作为只读数据在自定义view里使用，但是无法作为 v-model 这样的双向绑定



props还可以加一些限制

```vue
export default {
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



### 双向绑定值

v-model在自定义组件上，也会被翻译

```vue
<!-- 使用组件 -->
<CustomInput v-model="searchText" />
<!-- 等同于 -->
<CustomInput
  :modelValue="searchText"
  @update:modelValue="newValue => searchText = newValue"
/>

<!-- 申明组件 CustomInput.vue -->
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
```

modelValue这种相当于系统默认写法，会自动绑定到v-model上



子组件视图 -> 父组件v-model数据，主要是利用 `$emit('update:modelValue')` 因此写法可以多种，比如利用 `computed` 属性或者 `watch` 监听等



当然以上是默认写法，也可以自定义v-model的key，替代默认的 `modelValue`

```vue
<UserName
  v-model:first-name="first"
  v-model:last-name="last"
/>

<script>
export default {
  props: {
    firstName: String,
    lastName: String
  },
  emits: ['update:firstName', 'update:lastName']
}
</script>

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
```





### 事件传递

子组件事件回调给父组件，通过 `$emit`

```vue
<!-- BlogPost.vue, 省略了 <script> -->
<script>
export default {
  props: ['title'],
  emits: ['enlarge-text']
}
</script>

<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
```

```vue
<BlogPost title="My journey with Vue" @enlarge-text="methodXX" />
```



### 组件的继承

* 对于安卓，子View继承父View后，继承通用样式后再定制修改

* 对于vue，不是继承，而是 `组合` ，借助插槽 `slot`



### 动态引用

除了直接 <组件名 /> 还可以 `<component :is="组件名">` 动态引入

```vue
export default {
  components: {
    Home,
    Posts,
    Archive
  },
  data() {
    return {
      currentTab: 'Home',
      tabs: ['Home', 'Posts', 'Archive']
    }
  }
}
</script>

<template>
  <div class="demo">
    <button
       v-for="tab in tabs"
       :key="tab"
       :class="['tab-button', { active: currentTab === tab }]"
       @click="currentTab = tab" >
      {{ tab }}
    </button>
	  <component :is="currentTab" class="tab"></component>
  </div>
</template>
```



### 注册

全局注册 or 单个vue文件内注册

尽量局部注册，很多处使用才考虑全局注册



### 属性透传

“透传 attribute” 指的是传递给一个组件，却没有被该组件声明为 [props](https://cn.vuejs.org/guide/components/props.html) 或 [emits](https://cn.vuejs.org/guide/components/events.html#defining-custom-events) 的 attribute 或者 `v-on` 事件监听器。最常见的例子就是 `class`、`style` 和 `id`。

当一个组件以单个元素为根作渲染时，透传的 attribute 会自动被添加到根元素上。

如果要指定元素渲染透传属性，使用 `v-bind="$attrs"` 实现



### 插槽

不同于其他OOP的继承，js组件使用组合的方式实现（标签嵌套，也可以理解为继承），利用 `插槽slot`

> 渲染作用域？



插槽同样可以类似props传递属性

```vue
<!-- <MyComponent> 的模板 -->
<div>
  <slot :text="greetingMessage" :count="1"></slot>
</div>

<MyComponent v-slot="slotProps">
  {{ slotProps.text }} {{ slotProps.count }}
</MyComponent>
```



### 依赖注入

为了解决prop逐级透传问题

然而，请注意这**不会**使注入保持响应性。



> 点击事件如何逐级透传？





## 逻辑复用

### 组合式函数

https://cn.vuejs.org/guide/reusability/composables.html#extracting-composables-for-code-organization

vue2是选项式函数，vue3两种都可以，建议组合式

选项式缺点是不同业务逻辑会被拆分到不同基础结构里（data、method等），组合式可以把逻辑写在一起，这样更方便封装。



### 自定义指令

> TODO



### 插件

> TODO



## 内置组件

> TODO



## 状态管理

https://cn.vuejs.org/guide/scaling-up/state-management.html

响应式UI的基础之一，vue在单文件下已经默认实现了。但多个视图共同依赖一个状态呢？



自行手动处理，or 使用框架 `Pinia`

https://pinia.vuejs.org/zh/



## 测试

https://cn.vuejs.org/guide/scaling-up/testing.html



## 服务端渲染



## 响应式原理

https://cn.vuejs.org/guide/extras/reactivity-in-depth.html#how-reactivity-works-in-vue



## 问题

### 跨域问题



为什么要？

浏览器引入同源策略主要是为了保护用户隐私和安全。如果浏览器没有同源策略，那么一个网页就可以访问另外一个网页中的任何资源，包括 Cookie、Session 等用户隐私数据。这样就会导致安全问题，例如恶意网站可以通过脚本攻击其他网站，窃取用户隐私数据。

因此，浏览器引入同源策略来限制跨域访问，保证浏览器中网页的安全性和隐私性。只有在同源的情况下，浏览器才允许网页访问其他网页中的资源。



在现在的前后端分离项目中，前端和后端往往是分别部署在不同的服务器上的，这样就容易出现跨域问题。解决方法一般是在前端服务器上配置代理，将前端请求转发到后端服务器上，使得前端请求和后端响应的域名相同，避免了跨域问题。
