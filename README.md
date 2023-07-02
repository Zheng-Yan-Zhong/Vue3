## Table of Contents
* [Component](#Component)
* [Props](#Props)
* [Setup](#Setup)
* [Dynamically binding](#Dynamic-binding)
* [Attribute](#Attribute)
* [Slots](#Slots)
* [Emit](#Emit)
* [Event](#Event)
* [V-model](#V-model)
* [Refs](#Refs)
* [Component "is"](#Component-“is”)
* [Expose](#Expose)
* [Watch](#Watch)
* [WatchEffect](#WatchEffect)
* [Computed](#Computed)
* [Lifecycle](#Lifecycle)

## Component
組件類似於模組的概念，當我們需要重複使用或是為了切割程式碼而切分，都是避免一次閱讀太多不必要的功能。

在Vue中我們需使用import 引入並且註冊才能渲染在template中
```javascript
<script>
import Child from '/src/components/Child.vue'
or
import Child from '@/components/Child.vue'
</script>
```

並且需要透過回傳一個物件的方式
```javascript
<script>
import Child from '/src/components/Child.vue'
or
import Child from '@/components/Child.vue'
</script>
export default {
  components: {
    ChildInput: ChildInput
  }
}
```
當然在JavaScript 本身語法中key與value為相同時則可以寫成：
```javascript
const title = "Hello";
const data = {
  title,
}
console.log(data) // "Hello"
```

所以上面的Comoponent我們註冊的key跟value都是相同則可以寫成：
```javascript
export default {
  components: {
    ChildInput,
  },
}
```
接著我們在template引入即可
```javascript
//App.vue
<template>
  <h2>Main page</h2>
  <Child />
</template>
```

---

## Props
Props為父組件傳遞資料給子組件的一種方式，也是組件化其中很重要的概念。

而Props通常為唯獨(read-only)，除非父子資料相互依類才會借助其他方式綁定並且通信。
```javascript
//App.vue
<template>
  <div>
    Main page
  </div>
  <Child title="title" />
</template>
```

我們在子組件中假如需要取得父組件的值，必須使用props定義(類似註冊的概念)，當然我們也可以定義其預設值，來讓我們可以避免空值造成意外的崩潰。
```javascript
//Child.vue
<template>
  {{title}}  
</template>
<script>
export default {
  props: {
    title: String
  }
// or
  props: {
      title: {
        type: String,
        default: ""
      }
    }
  }
</script>
```

---

## Setup
setup函式是Vue組件的資料入口點，也就是函式或是變數的定義區塊。
我們可以取得兩個參數
```javascript
setup(props, context) {
  console.log(context)
}
```

其中props可以取得從父組件中傳入的變數，而context則包含了三個屬性及一個函式。

![](https://hackmd.io/_uploads/BkVfRoRu3.png)

而我們在setup函式中定義的方法或是變數，若要綁定至template中，必須透過return回傳

```javascript
 setup() {
   const title = "Hello world";
   return {
     title
   }
 }
```

最後我們必須在script中透過物件的方式回傳給Vue
```javascript
<script>
export default {
  setup(props, context) {
    const title = "Hello world";
    return {
      title,
    };
  },
};
</script>
```

---

## Dynamically binding
```javascript=
const title = ref("Hello world");
```
```htmlembedded=
<input
  type="text"
  value="title" 
/>
```
我們會發現畫面中並不會顯示變數`title`中的值，

![](https://hackmd.io/_uploads/HkU9jvAO3.png)

需要使用`v-bind:` 簡寫`:`綁定該屬性
```htmlembedded!

<input
  type="text"
  v-bind:value="title" 
/>
```
or
```htmlembedded
<input
  type="text"
  :value="title" 
/>
```
![](https://hackmd.io/_uploads/ryywhP0dn.png)

---

## Attribute
```javascript
//App.vue
<template>
  <Child title="Hello world" style="margin-top: 10px" />
</template>
```

```javascript
//Child.vue
<script>
export default {
  setup(props, context) {
    console.log(context);
  },
};
</script>
```
圖中我們可以看到只要是透過屬性傳入組件中，都可以顯示在props的第二參數的attrs

![](https://hackmd.io/_uploads/SJr6D30Oh.png)

---
## Slots
```javascript
//App.vue
<Child>
  <div>I'm slot</div>
</Child>
```

```javascript
//Child.vue
<template>
  <div>
    <p>Hello I'm Child</p>
    <slot />
  </div>
</template>
```

![](https://hackmd.io/_uploads/HkaRY2R_2.png)

我們打印context並且看到slots有提供一個函式可以呼叫

![](https://hackmd.io/_uploads/SJzxshCun.png)

可以看到slots為陣列，也就是如果傳入超過一個元素slots會依序渲染陣列中的元素。

![](https://hackmd.io/_uploads/S1ISinCd3.png)

如果我們多傳入一個元素
```javascript
//APP.VUE
<Child>
  <div>I'm slot</div>
  <div>I'm slot2</div>
</Child>
```
![](https://hackmd.io/_uploads/HJV6jhCOn.png)

![](https://hackmd.io/_uploads/Bkuf220O2.png)

---

## Emit
* emit會觸發父組件傳遞的事件
* 可以在setup函式的第二參數取得
* 也可以使用defineEmit定義
* 父組件傳遞函式需要使用`@`綁定

```javascript
 setup(props, { emit }) {
  //   blocks
 }
```
我們可以用一個input的子組件例子來傳遞資料回父組件
```javascript
<!-- App.vue -->
<template>
//   使用 @ 綁定事件給子組件使用
  <ChildInput @onInput="onInput" :title="value" />
</template>
```
```javascript
// App.vue
setup() {
  const title = ref("");
  function onInput(data) {
    title.value = data;
  }
  return {
    title,
    onInput
  }
}

```
接著我們需要在每次input的時候使用emit觸發父組件的onInput事件
```html
<!-- ChildInput -->
<input
  type="text"
  :value="title"
  @input="(event) => onInput(event.target.value)"
/>
```

```javascript
//ChildInput
setup(props, { emit }) {
  function onInput(value) {
    emit("onInput", value);
  }
  return {
    onInput
  };
},
```

---

## Event
常見的event有以下：
```javascript
export interface Events {
    onDrag: DragEvent;
    onDrop: DragEvent;
    onFocus: FocusEvent;
    onBlur: FocusEvent;
    onChange: Event;
    onInput: Event;
    onSubmit: Event;
    onLoad: Event;
    onClick: MouseEvent;
    onMousedown: MouseEvent;
    onMousemove: MouseEvent;
    onMouseover: MouseEvent;
    ......
}
```
在Vue中我們可以使用`v-bind:` 簡寫 `:` 並且綁定Vue的event事件`onChange`，或是使用`v-on:` 簡寫 `@` 並且綁定原生HTML event事件

**Vue event事件**
```html
<input
    type="text"
    :value="title"
    :onChange="(event) => onChange(event.target.value)"
  />
```

**HTML 原生事件**
```html
<input
    type="text"
    :value="title"
    @change="(event) => onChange(event.target.value)"
  />
```

---

## V-model
* 動態綁定，不用宣告event事件
* v-model綁定props的時候，子組件呼叫emit必須加上前綴`update:`
```javascript
<input
  type="text"
  :value="title"
  @input="(event) => (title = event.target.value)"
/>
```

```javascript!
<input
  type="text"
  v-model="title"
/>
```

當然我們也可以綁定emit

```javascript
// App.vue
<template>
  <ChildInput v-model:title="title" />
</template>
```

```javascript
//ChildInput.vue
<input
  type="text"
  :value="title"
  @input="(event) => onInput(event.target.value)"
/>
```

```javascript
//ChildInput.vue
export default {
  props: {
    title: {
      type: String,
    },
  },
  setup(props, { emit }) {
    function onInput(value) {
      emit("update:title", value);
    }
    return {
      onInput,
    };
  },
};
```

---

## Refs
```javascript
<input type="text" ref="inputRef" />
```

如果要在頁面一開始載入就focus輸入框，最好是在掛載後再抓取ref
```javascript
const inputRef = ref(null);
onMounted(() => {
  inputRef.value.focus();
  console.log(inputRef)//可以抓到完整的元素細項
});
```

---

## Component "is"
Component標籤不區分大小寫，並且綁定 `:is` 可以類似動態渲染組件的概念
```javascript
<div v-for="item in tabs">
  <Component :is="item" />
</div>
```

```javascript
import First from "@/components/First.vue";
import Second from "@/components/Second.vue";
import Third from "@/components/Third.vue";

export default {
  setup() {
    const tabs = [First, Second, Third];
    return {tabs}
  }
}
```
![](https://hackmd.io/_uploads/Sk5Z6jRuh.png)

---

## Expose
可以在父組件使用ref獲取實例後，執行子組件暴露的方法或是變數。

與emit的差別在於emit可以呼叫父組件中的方法，也就是規則由父組件規定。而expose則是已經在組件中定義好規則，父組件只能引用其提供的方法或是變數。
```javascript
//Child.vue
<template>
  <div>
    {{ count }}
  </div>
</template>
<script>
import { ref } from "vue";
export default {
  setup(props, context) {
    const count = ref(0);
    function increase() {
      count.value = count.value + 1;
    }
    context.expose(increase);
    return {
      count,
    };
  },
};
</script>

```
接著我們在父組件中使用ref獲取該組件的實例，也包括expose出來的函式
```javascript
//App.vue
<Child ref="childRef" />
```

![](https://hackmd.io/_uploads/ryzWR3Cdh.png)

我們可以看到increase被綁在了實例中的value，所以我們呼叫value就會去呼叫increase
```javascript
//App.vue
setup() {
  const childRef = ref(null);
  childRef.value();
}
```

那如果我們需要expose不只一個可以這樣做：
```javascript
context.expose({increase, decrease}); //expose 一個物件
```

當然變成物件後在父組件就必須使用
```javascript
setup() {
  const childRef = ref(null);
  childRef.value.increase();
  childRef.value.decrease();
}
```

如果要連同count一起輸出也可以
```javascript
context.expose({ increase, decrease, count });
```

---

## Watch
有時候我們需要一個一個數值改變的時候做相對應的操作，這時候watch可以幫助我們

>這邊要注意一下，假如是點擊後數值改變做side effect，不應該使用watch監控，而應該寫在點擊的事件中。

```javascript
<script>
import { ref, watch } from "vue";
export default {
  setup() {
    const text = ref("");
    watch(
      () => text.value, //監控值
      () => {
        console.log("changed");
      }
    );
    return {
      text,
    };
  },
};
</script>
```
如果我們不想監控專一的值可以使用watch中的options
```javascript
watch(
  () => text,
  () => {
    console.log("changed");
  },
  { deep: true }
  );
```

---

## WatchEffect
watchEffect提供我們只需要在區塊中有取用到其變數，就會自動監控該值
```javascript
const text = ref("");
watchEffect(() => {
  console.log(text.value);
});
```

## Computed
Computed可以幫助記憶函式執行跟執行完後的值，也就是只要使用computed後等於被cached(被快取)，基本上就不是響應式的結果。

但是這種方式可以避免一些不用一直刷新的列表一直被強制刷新影響到使用者的效能。
```javascript
<template>
  <div>
    {{ today() }}
    <br />
    {{ text }}
    <br />
    <input type="text" v-model="text" />
  </div>
</template>```
```javascript
function today() {
   console.log("get timezone");
   return new Date();
 }
```

![](https://hackmd.io/_uploads/BJ5ao6A_2.gif)

使用computed
```javascript
<template>
  <div>
    {{ today }}
    <br />
    {{ text }}
    <br />
    <input type="text" v-model="text" />
  </div>
</template>```
```javascript
const today = computed(() => {
  console.log("get timezone");
  return new Date();
});
```

![](https://hackmd.io/_uploads/S1x0j6Aun.gif)


---

## Lifecyle
[Vue3 Lifecycle](https://vuejs.org/guide/essentials/lifecycle.html#registering-lifecycle-hooks)

![](https://hackmd.io/_uploads/Sy7zJ0R_3.png)
>https://vuejs.org/guide/essentials/lifecycle.html#lifecycle-diagram

* setup
* onBeforeMount
* onMounted
* onBeforeUpdate
* onUpdated
* onBeforeUnmount
* onUnmounted

```javascript
setup() {
  onMounted(() => console.log("onMounted"));
  onBeforeMount(() => console.log("onBeforeMount"));
  console.log("setup");
},
// console setup -> onBeforeMount -> onMounted
```

上面簡單介紹一下onBeforeUnmount跟onUnmouned

只要是unmounted相關的，會在頁面解除掛載也就是換頁或是組件的dom被移除，就會執行unmounted的函式

```javascript
onBeforeUnmount(() => alert("page closing"));
onUnmount(() => alert("page closed"));
```

