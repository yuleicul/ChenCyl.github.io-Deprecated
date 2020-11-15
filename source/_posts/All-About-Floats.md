---
title: 「阅读笔记」All About Floats
date: 2019-04-02 18:14:33
excerpt: 介绍 CSS 中的 Float 属性
categories: 阅读笔记
index_img: /assets/web-layout.png
banner_img:
tags: CSS
updated: 2019-04-02 18:14:33
---

# All About Floats

> 原文：<https://css-tricks.com/all-about-floats/>

作者将文章分为了四个部分：什么是 Float，Float 的用途， Float 产生的问题，如何清除 Float。第一部分里做将 web layout 里的 float 与 print layout 里的 text  wrap 做类比；第二部分介绍了 float 的主要用途是构建整个 web 页面的布局（同时也提到了现在比较常用也更强大的布局方法 Flaxbox 和 Grid）；第三部分介绍了 Float 产生的问题，其中最主要的是 Collapse；第四部分介绍了常用的 4 种清除 float 的方法。

## Extracts

### What is Float?

**Float** is a CSS positioning property. To understand its purpose and origin, we can look to print design. In a print layout, images may be set into the page such that text wraps around them as needed. This is commonly and appropriately called =="text wrap"==.

Setting the float on an element with CSS happens like this:

```css
#sidebar {
  float: right;			
}
```

There are four valid values for the float property. **Left** and **Right** float elements those directions respectively. **None** (the default) ensures the element will not float and **Inherit** which will assume the float value from that elements parent element.

### What are floats used for?

Aside from the simple example of wrapping text around images, floats can be used to create **entire web layouts**.

![web-layout](/assets/web-layout.png)

### The Great Collapse

One of the more bewildering things about working with floats is how they can affect the element that contains them (their "parent" element). ==If this parent element contained nothing but floated elements, the height of it would literally collapse to nothing==.

![collapse](/assets/collapse.png)

As anti-intuitive as collapsing seems to be, the alternative is worse. Consider this scenario: (塌陷的直观对立面更不好，看看下面的情况)

![why-we-collapse](/assets/whywecollapse.png)

If the block element on top were to have automatically expanded to accommodate the floated element, we would have ==an unnatural spacing break in the flow of text between paragraphs==, with no practical way of fixing it.

### Techniques for Clearing Floats

If you are in a situation where you always know what the succeeding element is going to be, you can apply the `clear: both;` 

- **The Empty Div Method** is, quite literally, an empty div. `<div style="clear: both;"></div>`. Sometimes you'll see a `<br>` element or some other random element used, but div is the most common because it has no browser default styling, doesn't have any special function, and is unlikely to be generically styled with CSS. 

- **The Overflow Method** relies on setting the overflow CSS property on a parent element. If this property is set to ==auto== or ==hidden== on the parent element, the parent will expand to contain the floats, effectively clearing it for succeeding elements. 

- The Easy Clearing Method uses a clever CSS pseudo **selector (:after\</cide>) **to clear floats. Rather than setting the overflow on the parent, you apply an additional class like "clearfix" to it. Then apply this CSS:

  ```css
  .clearfix:after {
      content: ".";
      visibility: hidden;
      display: block;
      height: 0;
      clear: both;
  }
  ```

（很多时候不想要「文本围绕」的方式，当你知道后续的元素是什么时可以把后续元素设置为 clear: both 使它出现在 float 元素下方而不是围绕，但是大多数时候不知道后续元素什么，所以需要以下 3 种方法：

1. 在 float 元素和非 float 元素中间插入一个空白的元素，设置样式为 clear: both
2. 将 float 元素装在父元素中，设置父元素的 overflow 为 auto 或 hidden
3. 使用[伪类选择器](<http://www.ruanyifeng.com/blog/2009/03/css_selectors.html>)来设置样式）


