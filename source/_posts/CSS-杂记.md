---
title: CSS 杂记
date: 2019-08-30 19:24:50
categories: Skills
index_img:
banner_img:
tags: CSS
updated: 2019-08-30 19:24:50
---

## 内联块之间的空格
`block`	被显示为块级元素，此元素前后会带有换行符.
`inline-block` 被显示为内联元素，元素前后没有换行符。但因为书写的原因会内联块之间会产生空格，消除间隙的方法是在父元素上设置：
```css
.container {
  font-size: 0;
  letter-space: -4px;
}
```

## IE 绝对定位问题

IE 中定位为 `absolute` 或 `relative` 时会位置可能会和 `chrome` 里的不同。

考虑到浏览器的问题，在使用这些定位时，上下和左右都要分别选取一个来赋值。另外，`vertical-align` 可以定义行内元素本身在父元素中的对齐方式，若不是 `span` 而是 `inline-block` 还可以将 `line-height` 调至和父元素的高相同，然后通过 `vertical-align: top` 来居中。

## 关于 label

如何在点击 `radio` 的 `label` 时也能触发选中？

```html
<label ...>
  <input type='radio' .../>
</label>
```
这里的 `label` 放在最外层的作用是扩大鼠标点击范围，无论是点击在文字还是 `input` 上都能够触发响应，当然如下通过 `for` 属性绑定 `input` 的 `id` 属性也可以实现

```html
<input id='t' type='radio'>
<label for='t'>点此</label>
```

前者被称为隐式链接，后者是显示链接，很明显前者不需要 `id`，肯定前者好

## 高度过渡动画

> `height` 从确定值到 `auto`，或从 `auto` 到确定值如何实现过渡动画？

在实现一个折叠面板时，`height` 需要从 `67px` 变化到 `auto`，为了实现过渡动画，尝试了将 `auto` 设置为子元素的高度，动画是有了，但是当屏幕分辨率改变时，面板高度本应该随内容的高度改变，但无奈高度已经定死。在这种情况下，只能考虑如何给确定值到 `auto` 的变化加动画了。
方法有两种，比较简单的一种是：先将高度设置为确定值，等待过渡动画时间间隔后，再将其设置为 `auto`，但因为 `setTimeout` 并不总是按照给定的时间间隔发生回调，所以可能会出现延迟问题。另一种比较精确的做法是，监听 `animationend` 或 `transitionend` 事件，这个比较复杂还没有尝试。
为了提高性能，还可以使用 `transform: translateZ(0)` 将动画单独跑在一个图层上，避免了大块的重绘重排。另外，`perspective` 属性可以用来定义当前视角的与 `x-y` 平面的垂直距离。

## CSS 画正方形

```scss
// 方法一
.square {
  width: 10vh;
  height: 10vh;
}

// 方法二
.square {
  width: 10%;
}
.square:after {
  content: "";
  display: block;
  padding-top: 100%;
}
```

## CSS border 画三角形
  - border-top-color: transparent

## CSS 选择器可以这么用
  - .myClass:hover + [Sibling] 
  - .myClass:hover::after

## 旋转
  - 块级元素
  - transition: transform .3s linear;
  - transform: rotate(180deg);