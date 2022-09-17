# 浅析Vue中的sync修饰符
sync是Vue中的一个修饰符，官方的意思是对prop进行的一个双向绑定。也就是说，sync修饰符的功能是：当一个子组件改变了一个来自外部（prop）的值时，这个值的变化也会绑定到父组件，使父组件中的值也进行更新。  
这里有一个例子能够帮助说明这一点，这里有一个Vue子组件：  
```javascript
// Child.vue

<template>
  <div class="child">
    {{money}}
    <button @click="$emit('update:money', money-100)">
      <span>花钱</span>
    </button>
  </div>
</template>

<script>
export default {
  props: ["money"]
};
</script>

```
这个子组件接受一个来自外部的money变量，这个子组件的需求是，当点击按钮时，money数据改变的同时，也伴随着父组件数据的改变，但是这一点对于Vue本身来说是不允许的，因为Vue规定了子组件不能直接修改父组件的数据，会报错。   
但是父组件用sync修饰符就可以很简单的做到这一点。 
```javascript
// App.vue

<template>
  <div class="app">
    App.vue 我现在有 {{total}}
    <hr>
    <Child :money.sync="total"/>
  </div>
</template>

<script>
import Child from "./Child.vue";
export default {
  data() {
    return { total: 10000 };
  },
  components: { Child: Child }
};
</script>
``` 
这里父组件用sync监听到了子组件上数值的变化，并进行双向绑定，同时修改了父子组件中的数据。