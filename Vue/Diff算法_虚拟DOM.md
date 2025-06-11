## 虚拟 DOM

### 什么是虚拟 DOM？

> 虚拟 DOM（Virtual DOM）是用 JavaScript 对象模拟真实 DOM 结构的技术，是对真实 DOM 的轻量描述。

#### 优势：

- 避免频繁直接操作真实 DOM，提升性能
- 跨平台（可以转为原生、Weex、小程序等）
- 提供更高层次抽象能力（如 JSX）

### 虚拟 DOM 的基本结构

```js
const vnode = {
  tag: "div",
  props: { id: "app" },
  children: [{ tag: "span", props: {}, children: ["Hello"] }],
};
```

## Diff 算法

### 基本比较规则

- 同层对比(不跨级)
- 同类型节点再对比属性和子节点
- 使用唯一 key 优化列表对比

#### **为什么 key**

> 在 `v-for` 中使用 `key` 可以让` diff 算法`更准确判断节点是否相同，避免无意义的删除和重建，提升性能。

### Diff 流程

```text
<!-- vue2 -->
patch(oldVNode, newVNode)
  ↓
如果节点不同：
  直接删除 oldVNode，添加 newVNode
如果节点相同（tag 和 key 相同）：
  patchVnode(oldVNode, newVNode)
    ↓
    更新属性（props）
    递归更新子节点
```

### 列表更新优化策略

Vue2 和 React 都采用了**双端对比算法**（双指针）：

- 从新旧列表两端开始比较
- 利用 key 快速定位可以复用的节点
- 移动、插入、删除最小化 DOM 操作

```text
新前 vs 旧前
新后 vs 旧后
新后 vs 旧前（=> 移动）
新前 vs 旧后（=> 移动）
```
