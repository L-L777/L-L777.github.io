---
title: 小程序中pureDataPattern优化设置
date: 2024-10-30 20:57:56
tags: ["小程序"]
categories: 
    - ["前端"]
    - ["小程序"]
top_img: https://img0.baidu.com/it/u=3291332156,2373920352&fm=253&fmt=auto&app=138&f=JPEG?w=890&h=500
cover: https://img2.baidu.com/it/u=534490904,3498620580&fm=253&fmt=auto&app=120&f=JPEG?w=750&h=500
---
# **`pureDataPattern`优化设置**

# **`pureDataPattern`**

`pureDataPattern` 是微信小程序的一项优化设置，旨在提高页面性能，特别是在数据频繁更新时。如果页面的数据变化不涉及与渲染相关的逻辑（例如更新纯粹的业务数据而不是视图），可以通过配置 `pureDataPattern` 来告知小程序引擎哪些数据不需要参与视图层的 diff（对比计算）。

### **作用**

通过 `pureDataPattern`，开发者可以声明某些数据字段为“纯数据”，这些字段的更新不会触发视图层的渲染过程，避免了不必要的性能开销。它主要用于减少数据变化导致的性能损耗，从而优化页面性能。

### **使用场景**

当某些数据是纯业务逻辑数据，更新时不影响页面视图渲染时，可以使用 `pureDataPattern`。例如，统计数据、缓存数据、后台任务中的状态等。

### **如何配置**

`pureDataPattern` 是通过在小程序的 `app.json`、`page.json` 或者 `component.json` 文件中配置的，通常针对页面或组件。

### **示例**

以下是一个简单的 `page.json` 配置示例：

```
json复制代码{
  "usingComponents": {},
  "pureDataPattern": "^_"  // 表示以 _ 开头的数据字段为纯数据
}
```

在此配置中，`pureDataPattern` 表示所有以 `_` 开头的数据字段会被认为是纯数据，不会影响视图渲染。

### **规则说明**

- `pureDataPattern` 是一个正则表达式，用于匹配那些不参与视图渲染的数据字段。
- 当符合正则表达式的字段发生变化时，视图层将忽略这些字段的更新，从而避免视图层不必要的性能开销。

### **配置示例**

如果我们在一个小程序页面的 `data` 中声明了以下数据：

```
javascript复制代码Page({
  data: {
    _somePrivateData: 123,  // 不需要视图更新的数据
    visibleData: 'This is visible'  // 会影响视图的数据
  },
  // 数据更新时，只有 visibleData 会触发视图更新
  updateData() {
    this.setData({
      _somePrivateData: Math.random(),
      visibleData: 'Updated visible data'
    });
  }
});
```

通过配置 `pureDataPattern`，像 `_somePrivateData` 这样以 `_` 开头的数据更新时不会引起视图的重新渲染，而 `visibleData` 的更新会触发视图更新。

### **注意事项**

- 纯数据字段的更新不会影响视图的重渲染，但如果需要该数据在视图中显示，必须确保它不符合 `pureDataPattern` 的规则。
- 使用 `pureDataPattern` 仅适用于特定场景，过度使用可能导致预期之外的行为，因为视图层无法感知到这些数据的变化。

### **总结**

`pureDataPattern` 是微信小程序的一项性能优化机制，用于声明不影响视图渲染的纯数据字段。合理使用可以减少不必要的视图更新，从而提升性能，尤其在处理频繁的数据变化时非常有用。