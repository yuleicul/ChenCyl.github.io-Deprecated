---
title: React DnD 入门
date: 2020-12-06 17:15:06
categories: Skills
index_img: /assets/react-dnd.assets/index.png
banner_img:
tags: React
updated:
---

这篇文章的诞生原因是实际项目中有拖放的需求，导师推荐我使用 React DnD，在使用的过程我产生了一些感悟，在此分享给大家。

## 关于拖放

### 随处可见的拖放

拖放，是生活中最常见的交互之一。在手机上，拖动应用图标改变它的位置；在电脑上，拖动文件到垃圾桶来删除它；包括，我最喜欢 QQ 的一个功能：拖动消息徽标表示已读。

### 前端拖放的发展

在 HTML5 Drag and Drop（后文简称 DnD） API 诞生之前，开发者常用的实现拖放的方法是监听鼠标事件，一个完整的拖放操作往往需要监听 mousedown、mousemove 和 mouseup 事件。HTML5 DnD API 诞生定义了一系列的拖拽事件，例如 drag、dragenter、dragover 等。除此之外，它还新增了一些特性，比如拖拽图像，即拖拽时跟随鼠标图像。我们可以想象在原生 API 出现之前开发者如果想要实现「拖拽图像」，大概会去监听鼠标的 mousemove 事件，根据鼠标移动的位置去实时地渲染一个跟随鼠标的图形。现在就不需要这样做了，原生 API 已经给我们提供了这样的支持。

除了拖拽图像，我们还可以通过 API 定义拖拽的效果。什么是拖拽的效果呢？在 Chrome 下，当把一个[链接](https://developer.mozilla.org/zh-CN/docs/Web/API/HTML_Drag_and_Drop_API)拖拽到地址栏的时候，鼠标下方会出现一个加号，这就是拖拽效果。加号代表的拖拽效果是 「copy」。这样的交互不光在浏览器中有，操作系统里其实也有。你可以做这样一个操作，在电脑桌面拖拽一个文件，同时按住 Alt/Option，此时鼠标下出现了加号，松开鼠标，就成功复制了一份文件。

我们可以在[这里](https://developer.mozilla.org/zh-CN/docs/Web/API/HTML_Drag_and_Drop_API)查看有关 HTML DnD 的相关 API。

## React DnD 是什么

如果你正在使用 React，正好需要实现拖放这种交互，那 React-DnD 会是一个很不错的选择。为什么呢[^1]？

### React DnD 的特性

第一，它非常适合组件化的应用；

第二，它和 React 的心智模型高度重合，声明式且不直接操作 DOM；

第三，它填补了原生 Drag & Drop API 的设计缺陷，磨平了浏览器间的不一致性。虽然之前提到了很多原生 API 的比起鼠标事件的优点，但不可否决地它也存在很多缺陷。在这里推荐一篇[文章](https://quirksmode.org/blog/archives/2009/09/the_html5_drag.html)，感兴趣的话可以去这篇文章里全文搜索 fucking 来感受一下作者在使用原生 API 过程中的愤怒。

第四，它是可拓展和可测试的。React DnD 的不光可以用 HTML5 DnD 的 API 来实现的，它可以接受自定义的底层实现。例如可以基于触摸事件、鼠标事件来自定义底层的逻辑，然后再根据它接口的要求封装一层，这在 React-DnD 里叫做自定义的 backend。官方提供了 3 个 backend[^2]，分别用于鼠标拖放、触摸拖放和测试，测试用的 backend 可以让拖放的交互出现在 node 环境中。

### What does It Look Like？

先来感受一下 React DnD 在使用的时候长什么样子，[戳这里](https://codesandbox.io/s/github/react-dnd/react-dnd/tree/gh-pages/examples_hooks_ts/01-dustbin/single-target?from-embed)。

### React DnD 的定义

带着对 React DnD 的印象，我们看一下它的定义：

> React DnD is a set of React utilities to help you build complex drag and drop interfaces while keeping your components decoupled.[^3] 

React DnD 是一系列 React 工具的集合，它能帮助我们构建复杂的拖放交互，并且让组件保持解耦状态。

## Monitor

接下来介绍 React DnD 里非常重要的一个概念，monitor。

### 监视器

拖放是一种有内部状态的交互，比如是否正在拖拽、正在拖拽什么东西、拖拽的位置、是否可以放置、是否已经放置了，我们如何拿到 React DnD 内部的这些状态？答案是通过 Monitor[^4]。

> Monitor（监视器、监控），正如它的名字一样，开发者可以透过监视器观察到内部状态。

### 收集状态

具体怎么拿呢？请看下图（起点为打工人）。

![collect-state](/assets/react-dnd.assets/image-20201206164455871.png)

上图可以归纳为两步：

1. 通过 collect 函数收集 monitor 暴露的状态；
2. 将 collect 函数传给 hook，hook 返回状态。

代码如下：

```js
function collect(monitor) {
  return {
    highlighted: monitor.canDrop(),
    hovered: monitor.isOver()
  }
}

const [{ highlighted, hovered }, drag] = useDrag({
  item: {...},
  collect
})
```

在[这里](https://react-dnd.github.io/react-dnd/docs/api/drag-source-monitor)我们可以看到 monitor 暴露的全部状态。

## React DnD API

下面介绍 React DnD 最主要的三个 API：DndProvider、useDrag 和 useHook。如下图，我们有两个组件 Drag Source 拖拽源和 Drop Target 放置目标，他们被 DndProvider 包裹。组件内部分别调用对应的 hook，拖拽源会调用 useDrag，放置目标会调用 useDrop。

![react-dnd-api](/assets/react-dnd.assets/P89XihYAEcqvupUcvpqKwXwQ1CWrqMGo6ACRNUtmJOE1Lx3cngmXBqR7LiW02KL0CeKqVD9Uj3WGcacEh_mD_i1XZQVrqI4HRsn9y9Y49Cyd4_q_vSTOK6vN60w8SsNbe0crvAlO0y0.png)

### DndProvider

DndProvider 需要属性 backend，[之前](#react-dnd-的特性)我有提到过 backend，它是对底层 API 的一层封装，比如这里用的 html5-backend 就是对 html5 拖放 API 的一层封装。React DnD 把它设计成这种类似插件的、可插拔（pluggable）的形式，便于让开发者自定义 backend。

```jsx
import { HTML5Backend } from 'react-dnd-html5-backend'
import { DndProvider } from 'react-dnd'

export default class YourApp {
  render() {
    return (
      <DndProvider backend={HTML5Backend}>
        /* Your Drag-and-Drop Application */
      </DndProvider>
    )
  }
}
```

### useDrag

A hook to use the current component as a drag source.

```jsx
import { useDrag } from 'react-dnd'

function DraggableComponent(props) {
  const [{ isDragging }, drag] = useDrag({
    item: { type: 'Box', ... },
    begin: (monitor) => {},
    end: (item, monitor) => {},
    collect: (monitor) => ({
      isDragging: monitor.isDragging()
    })
  })
  return <div ref={drag}>Drag Source</div>
}
```

| 参数字段         | 说明                                     | 是否必须 | 类型                                                         |
| ---------------- | ---------------------------------------- | -------- | ------------------------------------------------------------ |
| item             | 拖拽源，可自定数据，但 type 必须有       | 是       | object (DragObject)                                          |
| \|--- type       | 拖拽源的类型                             | 是       | string \| symbol                                             |
| begin            | 拖动开始时被调用，返回的对象会覆盖 item  | -        | (monitor: DragSourceMonitor) => DragObject \| undefined \| void |
| end              | 拖动停止时被调用                         | -        | (draggedItem: DragObject \| undefined, monitor: DragSourceMonitor) => void |
| canDrag          | 覆盖默认行为，定义拖拽源是否允许被拖动， | -        | boolean \| ((monitor: DragSourceMonitor) => boolean)         |
| isDragging       | 覆盖默认行为，定义拖拽源是否正在被拖动   | -        | (monitor: DragSourceMonitor) => boolean                      |
| collect          | 收集函数，可收集内部状态                 | -        | (monitor: DragSourceMonitor) => CollectedProps               |
| options          | The drag source options                  | -        | object                                                       |
| \|--- dropEffect | 拖放效果                                 | -        | string                                                       |
| previewOptions   | DragPreview options                      | -        | object                                                       |

### useDrop

A hook to use the current component as a drop target.

```jsx
import { useDrop } from 'react-dnd'

function myDropTarget(props) {
  const [{ canDrop }, drop] = useDrop({
    accept: 'Box',
    drop: (item, monitor) => {},
    collect: (monitor) => {
    	canDrop: monitor.canDrop()
    }
  })

  return <div ref={drop}>Drop Target</div>
}
```

| 参数字段 | 说明                                                         | 是否必须 | 类型                                                         |
| -------- | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| accept   | 拖拽源的类型                                                 | 是       | string \| symbol                                             |
| drop     | 放置时被调用，返回的对象可以通过 monitor.getDropResult() 得到 | -        | (item: DragObject, monitor: DropTargetMonitor) => DropResult \| undefined |
| hover    | 悬停时被调用                                                 | -        | (item: DragObject, monitor: DropTargetMonitor) => void       |
| canDrop  | 覆盖默认行为，定义放置目标是否允许放置。                     | -        | (item: DragObject, monitor: DropTargetMonitor) => boolean    |
| collect  | 收集函数，可收集内部状态                                     | -        | (monitor: DropTargetMonitor) => CollectedProps               |
| options  | The drop target optinos                                      | -        | any                                                          |

### 思考

看完 React DnD 的 API 之后，不知道你有没有产生疑问，它的 API 为什么要这么设计呢？引用 React DnD 文档里的话[^3]，这种设计在第一次使用时会让人望而却步（intimidating），那他为什么仍然要这么设计呢？这个问题留给你去思考。

好的，此时再来看看刚刚的那个[例子](https://codesandbox.io/s/github/react-dnd/react-dnd/tree/gh-pages/examples_hooks_ts/01-dustbin/single-target?from-embed)，这个时候我们已经可以清楚地知道它在干嘛了（响指）。

## React DnD 能做什么

去[这里](https://react-dnd.github.io/react-dnd/examples)探索 React DnD 的各种用法吧。

## 最后

通过以上的介绍，你大概已经认识了 React DnD，认识了 HTML 原生的拖放 API，重新感受了拖放这种交互。

你有没有想过，如果没有拖放，我们每天面对的网页会变成什么样？

当我们再也不能拖拽 Slide 来改变它的顺序，再也不能通过拖拽图片将它复制到 Slide 里，再也不能拖出一个标签页让它变成新的窗口，再也不能拖动 jira 单来改变它的状态，当这些我们都不能做的时候，我们的效率和乐趣都会大幅度下降，是否会觉得遗憾和可惜？（肯定会吧——）

所以，我们已经离不开拖放了。作为一名前端工程师，也许有一天你要去设计和实现一个拖放，希望那个时候你可以想起 React DnD。

## 补充

- [Dan Abramov](https://github.com/gaearon) 是 React DnD 的贡献者之一 
- [react-beautiful-dnd](https://github.com/atlassian/react-beautiful-dnd)，另一个很优秀的拖拽库，由 Atlassian 主导。与 React DnD 有不同的设计原则 
- [Adventure time](https://en.wikipedia.org/wiki/Adventure_Time)，在 react-dnd 和 react-beautiful-dnd 文档中都出现的元素，一部动画片

## Reference

[^1]: React DnD features, https://react-dnd.github.io/react-dnd/about#features
[^2]: React DnD backends, https://react-dnd.github.io/react-dnd/docs/backends/html5
[^3]: React DnD overview, https://react-dnd.github.io/react-dnd/docs/overview
[^4]: React DnD monitor, https://react-dnd.github.io/react-dnd/docs/overview#monitors