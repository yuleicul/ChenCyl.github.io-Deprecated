---
title: A Complete Guide to Flexbox
date: 2019-04-04 18:17:42
excerpt: 介绍 CSS 中的 Flexbox 布局
categories: 阅读笔记
index_img: /assets/00-basic-terminology.svg
banner_img:
tags: CSS
updated: 2019-04-04 18:17:42
---

> 原文：<https://css-tricks.com/snippets/css/a-guide-to-flexbox/>

## Background

The `Flexbox Layout` (Flexible Box) module ([a W3C Candidate Recommendation](https://www.w3.org/TR/css-flexbox/) as of October 2017) aims at providing a more efficient way to lay out, align and distribute space among items in a container, even when their size is unknown and/or dynamic (thus the word "flex").

## Basics & Terminology

If "regular" layout is based on both block and inline flow directions, the flex layout is based on "flex-flow directions". ![A diagram explaining flexbox terminology. The size across the main axis of flexbox is called the main size, the other direction is the cross size. Those sizes have a main start, main end, cross start, and cross end.](/assets/00-basic-terminology.svg)

## Properties for the Parent (flex container)

| property        | value                                                        |                                                              |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| display         | flex                                                         |                                                              |
| flex-direction  | row / row-reverse / column / colume-reverse                  | ![the four possible values of flex-direction being shown: top to bottom, bottom to top, right to left, and left to right](/assets/flex-direction.svg) |
| flex-wrap       | nowrap / wrap / wrap-reverse                                 | ![two rows of boxes, the first wrapping down onto the second](/assets/flex-wrap.svg) |
| flex-flow       | <‘flex-direction’> <‘flex-wrap’>                             |                                                              |
| justify-content | flex-start / flex-end / center / space-between / space-around / space-evenly | ![flex items within a flex container demonstrating the different spacing options](/assets/justify-content.svg) |
| align-items     | stretch / flex-start / flex-end / center / baseline          | ![demonstration of differnet alignment options, like all boxes stuck to the top of a flex parent, the bottom, stretched out, or along a baseline](/assets/align-items.svg) |
| align-content   | flex-start / flex-end / center / space-between / space-around / stretchflex-direction | ![examples of the align-content property where a group of items cluster at the top or bottom, or stretch out to fill the space, or have spacing.](/assets/align-content.svg) |

## Properties for the Children (flex items)

==Note that `float`, `clear` and `vertical-align` have no effect on a flex item.==

| property    | value                                                        |                                                              |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| order       | \<integer>; */\* default is 0 \*/*                           | ![Diagram showing flexbox order. A container with the items being 1 1 1 2 3, -1 1 2 5, and 2 2 99.](/assets/order.svg) |
| flex-grow   | \<number>; */\* default 0 \*/*                               | ![two rows of items, the first has all equally sized items with equal flex-grow numbers, the second with the center item at twice the width because it's value is 2 instead of 1.](/assets/flex-grow.svg) |
| flex-shrink | \<number>; */\* default 1 \*/*                               |                                                              |
| flex-basis  | \<length> or auto; /\* default auto \*/                      |                                                              |
| flex        | This is the shorthand for `flex-grow,` `flex-shrink` and `flex-basis `. Default is `0 1 auto`. |                                                              |
| align-self  | auto / stretch / flex-start / flex-end / center / baseline   | ![One item with a align-self value is positioned along the bottom of a flex parent instead of the top where all the rest of the items are.](/assets/align-self.svg) |

## Examples

eg1:

```css
.parent {
  display: flex;
  height: 300px; /* Or whatever */
}

.child {
  width: 100px;  /* Or whatever */
  height: 100px; /* Or whatever */
  margin: auto;  /* Magic! */
}
```

This relies on the fact a margin set to `auto` in a flex container absorb extra space. So setting a vertical margin of `auto` will make the item ==perfectly centered== in both axis.

eg2: [Codepen](<https://css-tricks.com/snippets/css/a-guide-to-flexbox/>)

布局跟随屏幕尺寸变化

```css
.wrapper {
  display: flex;
  flex-flow: row wrap;
}
/* 屏幕 600px 以下 */
.wrapper > * {
  flex: 1 100%;
}
/* Medium screens */
@media all and (min-width: 600px) {
  /* We tell both sidebars to share a row */
  .aside { flex: 1 auto; }
}
/* Large screens */
@media all and (min-width: 800px) {
  /* We invert order of first sidebar and main
   * And tell the main element to take twice as much width as the other two sidebars 
   */
  .main { flex: 2 0px; }
  .aside-1 { order: 1; }
  .main    { order: 2; }
  .aside-2 { order: 3; }
  .footer  { order: 4; }
}
```

---

About `flex-basic` , this is what I don't understand.

![image-20190404185821800](/assets/image-20190404185821800.png)
