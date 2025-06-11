## setup 函数详解与生命周期替代

## reactive vs ref，toRefs, toRef 的区别与使用场景

## 自定义 Hook（组合式函数）如何组织逻辑

## defineExpose, defineProps 使用技巧

## script setup 写法语法糖

## 使用

```js
import { ref, reactive, computed, watch, onMounted } from "vue";

export default {
  setup(props, context) {
    const count = ref(0);
    const user = reactive({ name: "七七", age: 25 });

    const double = computed(() => count.value * 2);

    watch(
      () => user.name,
      (newVal, oldVal) => {
        console.log(`Name changed from ${oldVal} to ${newVal}`);
      },
    );

    onMounted(() => {
      console.log("Component mounted");
    });

    const increment = () => count.value++;

    return {
      count,
      double,
      user,
      increment,
    };
  },
};
```
