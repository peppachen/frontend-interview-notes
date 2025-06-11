## Vuex 与 Pinia 状态管理对比

| 特性            | Vuex                               | Pinia                          |
| --------------- | ---------------------------------- | ------------------------------ |
| 状态定义        | 使用 state 对象                    | 使用 state() 函数              |
| 修改状态方式    | mutations（同步）+ actions（异步） | 直接修改或通过 actions         |
| 模块化          | 支持模块，但较复杂                 | 推荐使用多个 Store，逻辑更清晰 |
| TypeScript 支持 | 类型推导较复杂                     | 原生支持 TS，类型推导优秀      |
| DevTools 支持   | 支持                               | 更好，官方集成 Vue Devtools    |

## 使用方式

### Vuex 简单使用

```js
// store.js
import Vue from "vue";
import Vuex from "vuex";

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    count: 0,
  },
  mutations: {
    increment(state) {
      state.count++;
    },
  },
  actions: {
    asyncIncrement({ commit }) {
      setTimeout(() => {
        commit("increment");
      }, 1000);
    },
  },
});
```

### Pinia 简单使用

```ts
// store/counter.ts
import { defineStore } from "pinia";

export const useCounterStore = defineStore("counter", {
  state: () => ({ count: 0 }),
  actions: {
    increment() {
      this.count++;
    },
    async asyncIncrement() {
      setTimeout(() => {
        this.increment();
      }, 1000);
    },
  },
});

// main.ts
import { createApp } from "vue";
import { createPinia } from "pinia";
import App from "./App.vue";

const app = createApp(App);
app.use(createPinia());
app.mount("#app");
```

## 状态管理最佳实践

- 使用模块/Store 分离业务逻辑：保持每个模块职责单一，易于维护
- 封装请求逻辑进 action：方便测试与复用，避免组件内重复逻辑
- 持久化状态：使用 pinia-plugin-persistedstate 或 vuex-persistedstate
- 严格区分响应式数据与只读配置数据
- 结合组合式 API 使用 Store

## 插件推荐

- pinia-plugin-persistedstate：状态持久化
- vuex-persistedstate：本地存储同步 Vuex
- pinia-logger：开发调试状态变化日志

## 状态管理设计建议

- 项目小可不引入状态管理工具，使用 props/emits 即可
- 项目中后期或多人协作时再统一引入状态管理
- 状态变更必须可追踪（DevTools 支持）

## 迁移指南：Vuex → Pinia

- 将 Vuex 中的模块拆分为多个 defineStore
- 移除 mutation，仅使用 action 管理逻辑
- 用组合式方式访问 state/getters/actions

```ts
const counter = useCounterStore();
counter.count++;
counter.increment();
```

> Pinia 是 Vue 官方推荐的下一代状态管理工具，强类型支持、开发体验优于 Vuex，适合新项目优先使用。
