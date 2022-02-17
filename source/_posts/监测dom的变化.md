---
title: P4 监测DOM的属性变化
date: 2021-11-15 10:11:16
tags:
  - 工作记录
categories: [工作记录]
---

# 监测DOM的属性变化

### 有一些情况比如说dom属性如宽度，高度发生变化之后进行一些逻辑操作，就可以observer之后执行。自动监测dom的属性变化

```javascript

observerEchartDom(dom) {
  const MutationObserver =
    window.MutationObserver ||
    window.WebKitMutationObserver ||
    window.MozMutationObserver
  this.observer = new MutationObserver((mutation) => {
    console.log('asdf', mutation)
  })
  this.observer.observe(dom, {
    attributes: true,
    attributeFilter: ['style'],
    attributeOldValue: true,
  })
},


```
