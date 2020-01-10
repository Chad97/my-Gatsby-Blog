---
title: '小程序数据监听(observers),避免赋值死循环'
date: 2019-11-14 17:19:34
tags: web-other
categories: 《web-other》
---



> 在小程序开发过程中，我们通过 observers 监听处理 properties 组件数学列表的过程中，免不了对 当前数据进行处理 
> 在我们对监听的数据进行赋值时候，会造成死循环，其实这个问题在大多数框架中都有这个问题。

- 小栗子🌰[错误示范]
```js
   properties: {
    playlist: {
      type: Object
    }
  },
  // 数据监听
  observers: {
    ['playlist.playCount'] (count) {
      this.setData({
        ['playlist.playCount']: this._tranNumber(count, 2)
      })
    }
  },
```
- 小栗子🌰[正确示范]

```js

data: {

  // 数据监听
  observers: {
    ['playlist.playCount'] (count) {
      this.setData({
        _count: this._tranNumber(count, 2)
      })
    }
  },
    // 避免监听器 赋值死循环
    _count: 0
  },
```


 - [ ] 原因就是 `playlist.playCount` 这 个数据是监听状态 如果 `playlist.playCount` 改变就会触发这个方法，如果在setData 中给原数据赋值 他就会不断的触发这个方法，从而导致死循环

- [x] 所以解决方案就是 在组件 data 中定义一个 变量 接受这个新的值，然后在页面中使用 data中的值 在这里也就是 `_count`