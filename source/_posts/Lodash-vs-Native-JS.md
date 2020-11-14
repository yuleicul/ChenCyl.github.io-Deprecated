---
title: Lodash vs. Native JS
date: 2019-07-19 19:08:20
excerpt: Lodash vs. Native JS
categories: Skills
index_img: /assets/lodash.svg
banner_img:
tags: JavaScript
updated: 2019-07-19 19:08:20
---

## Native JS

> 不用 Lodash 也完全 ok 的情况

- Array.prototype.flat() / Array.prototype.flat(Infinity)
  ```js
  [1, [2, [3]]].flat() // [1, 2, [3]] 
  [1, [2, [3]]].flat(Infinity) // [1, 2, 3]
  ```
- Array.prototype.includes() (=indexOf)
  ```js
  [1, 2, 3].includes(1) // true
  ```
- Array.isArray()
- Array.prototype.lastIndexOf()
- Array.prototype.reverse()
- Array.prototype.every([ function ])
  ```js
  function isLargerThanTen (element, index, array) {
    return element >= 10
  }

  [10, 20, 30].every(isLargerThanTen) // true
  ```
- Array.prototype.reduceRight(): 从右到左的 reduce()
- Array.prototype.some([ function ])
  ```js
  function isLargerThanTen (element, index, array) {
    return element >= 10
  }

  [10, 9, 8].some(isLargerThanTen) // true
  ```
-  `[...new Set(array)]` == _.uniq
- Number.isFinite()：是否是有限的数
- Object.values()
- String.prototype.padStart() / String.prototype.padEnd()
  ```js
  '123'.padStart(5, '0')) // '00123'

  '123'.padEnd(5, '0')) // '12300'
  ```
## Lodash

> 用 Lodash 更方便的情况

- _.chunk
  ```js
  _.chunk(['a', 'b', 'c', 'd'], 2) // => [['a', 'b'], ['c', 'd']]
  _.chunk(['a', 'b', 'c', 'd'], 3) // => [['a', 'b', 'c'], ['d']]
  ```
- _.difference
  ```js
  _.difference([1, 2, 3, 4, 5], [5, 2, 10]) // => [1, 3, 4]
  ```
- _.fromPairs
  ```js
  _.fromPairs([['a', 1], ['b', 2]]) // => { 'a': 1, 'b': 2 }
  ```
- _.intersection
  ```js
  _.intersection([1, 2, 3], [101, 2, 1, 10], [2, 1]) // => [1, 2]
  ```
- _groupBy
  ```js
  _.groupBy(['one', 'two', 'three'], 'length') 
  // => {3: ["one", "two"], 5: ["three"]}
  ```
- _range
  ```js
  _.range(4)  // [0, 1, 2, 3]
  _.range(-4) // [0, -1, -2, -3]
  _.range(1, 5)     // [1, 2, 3, 4]
  _.range(0, 20, 5) // [0, 5, 10, 15]
  ```
- _.sample / _random
  ```js
  _.sample([0, 1, 2, 3, 4]) // 2 (随机)

  _.random(0, 5);
  // => an integer between 0 and 5
  
  _.random(5);
  // => also an integer between 0 and 5
  
  _.random(5, true);
  // => a floating-point number between 0 and 5
  
  _.random(1.2, 5.2);
  // => a floating-point number between 1.2 and 5.2
  ```
- _.isEmpty
  ```js
  // ! Note this is not evaluating a Set or a Map

  _.isEmpty(null) // true
  _.isEmpty('') // true
  _.isEmpty({}) // true
  _.isEmpty([]) // true
  _.isEmpty({a: '1'}) // false
  ```
- _.upperFIrst
  ```js
  _.upperFirst('george') // 'George'
  ```
