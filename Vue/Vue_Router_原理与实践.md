## 路由模式

### hash 模式（默认）

- 地址中带 `#`，如：`/#/home`
- 利用 `window.onhashchange` 事件监听
- 无需服务器配置，兼容性好

### history 模式（推荐）

- 地址更简洁，如：`/home`
- 基于 HTML5 的 History API（`pushState` / `replaceState`）
- 需服务端配置支持（避免 404）

## 路由匹配原理

### 注册表结构

- 每个路由配置对象对应一个记录，生成路径和组件的映射表
- Vue Router 内部通过 path-to-regexp 将路由规则编译为正则表达式进行匹配

### 匹配过程

1. 浏览器地址栏变更（或代码跳转）
2. Vue Router 从路由表中查找符合的路径
3. 渲染对应的组件到 `<router-view>`

## 动态路由 & 嵌套路由

### 动态路由

```ts
{
  path: '/user/:id',
  component: User
}
```

```vue
<template>
  <div>User ID: {{ $route.params.id }}</div>
</template>
```

### 嵌套路由

```ts
{
  path: '/dashboard',
  component: Dashboard,
  children: [
    { path: 'stats', component: Stats },
    { path: 'settings', component: Settings }
  ]
}
```

## 路由懒加载

- **原理**：利用 JavaScript 的 `import()` 动态导入语法，将每个路由组件单独打包为一个 chunk，按需加载。
- **好处**：减小初始包体积，加快首页加载速度。
- **底层依赖**：
  - `Webpack/Vite` 等构建工具会自动将其分割为独立 `chunk` 文件
  - 首次访问该路由时才发起请求加载对应模块

```ts
// 传统加载方式：
import Home from "./views/Home.vue";
// 懒加载方式（推荐）：
const Home = () => import("./views/Home.vue");
```

## 路由守卫

### 全局守卫(beforeEach)

```ts
router.beforeEach((to, from, next) => {
  if (to.meta.auth && !isLogin()) {
    next("/login");
  } else {
    next();
  }
});
```

### 路由独享守卫(beforeEnter)

```ts
{
  path: '/admin',
  beforeEnter: (to, from, next) => { ... }
}
```

### 组件内守卫

```ts
beforeRouteEnter(to, from, next) { ... }
beforeRouteUpdate(to, from, next) { ... }
beforeRouteLeave(to, from, next) { ... }
```

## 滚动行为控制

```ts
const router = new VueRouter({
  routes,
  scrollBehavior(to, from, savedPosition) {
    return savedPosition || { top: 0 };
  },
});
```

## Vue Router 实践技巧

- 使用 meta 属性实现权限控制、标题设置等
- 利用 router.addRoute 实现动态添加路由（常用于权限路由）
- 搭配 keep-alive 缓存页面状态
- 使用 replace 替代 push 避免历史记录堆积
- 利用 router-view 的 name 属性实现多个视图出口

## Vue Router 与 Vue3 的变化

- 支持组合式 API
- 支持异步路由组件（如 defineAsyncComponent）
- 可返回异步导航守卫（支持 await）

```ts
import { createRouter, createWebHistory } from "vue-router";

const router = createRouter({
  history: createWebHistory(),
  routes,
});
```

## 编程式导航和参数传递技巧

### 1. 编程式导航基本用法

#### `this.$router.push`

```js
// 字符串路径
this.$router.push("/home");

// 对象写法
this.$router.push({ path: "/home" });

// 替换当前历史记录，不会留下返回记录
this.$router.replace("/login");
// 返回上一页
this.$router.go(-1);
```

### 2. 命名路由传参(推荐)

```js
// 配置
{
  path: '/user/:id',
  name: 'User',
  component: UserPage
}

//使用 - 注意：使用 name + params 时不能同时使用 path。
this.$router.push({ name: 'User', params: { id: 123 } });

```

### 3. query 参数传递

```js
// 路由配置不需要改变
this.$router.push({ path: "/search", query: { keyword: "vue" } });

// 获取参数
this.$route.query.keyword; // 'vue'
```

### 4. params 参数传递

```js
// **注意**
// 1. 使用 params 必须搭配命名路由
this.$router.push({ name: "User", params: { id: 1 } }); // ✅
// 2. 防止重复导航报错
const to = { name: "User", params: { id: 1 } };
if (to.name !== this.$route.name || to.params.id !== this.$route.params.id) {
  this.$router.push(to);
}
// 组合式
// import { useRouter } from 'vue-router';

// const router = useRouter();
// router.push({ name: 'User', params: { id: 1 } });
```

```js

```

## 在组件内部控制导航

1. 使用`$router`进行跳转

```ts
this.$router.push("/about"); // 添加一条历史记录
this.$router.replace("/about"); // 替换当前历史记录
this.$router.go(-1); // 后退

// 带参数跳转
this.$router.push({ name: "user", params: { id: 123 } });
```

2. Vue3 中(组合式 API)

```ts
import { useRouter } from "vue-router";

const router = useRouter();
router.push("/home");
```

## 页面(组件)缓存与状态恢复

1. 使用<keep-alive>缓存组件状态

```vue
<keep-alive>
  <router-view v-if="keepAlive"></router-view>
</keep-alive>
```

2. 配置路由 meta 字段

```ts
{
  path: '/list',
  component: List,
  meta: { keepAlive: true }
}
```

```vue
<keep-alive include="List">
  <router-view></router-view>
</keep-alive>
```

3. Vue3 + 动态判断缓存组件名：

```vue
<keep-alive :include="cachedViews">
  <router-view></router-view>
</keep-alive>
```

## 路由变化时组件状态保留的方法

1. 使用 <keep-alive> 保持组件缓存（同上）
2. 通过 `activated / deactivated` 生命周期管理状态：

```ts
export default {
  activated() {
    console.log("组件被激活");
  },
  deactivated() {
    console.log("组件被缓存");
  },
};
```

3. 将组件内部状态提升到 Vuex/Pinia，路由切换不会丢失状态。
4. 统一缓存策略 + 动态控制缓存清除

- 使用 route 的 name 字段配合 <keep-alive include> 精准控制
- 用全局状态控制哪些页面需要缓存
- 用自定义 router-view 组件封装缓存逻辑
