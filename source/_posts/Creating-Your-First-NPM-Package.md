---
title: Creating Your First NPM Package | English Reading
date: 2019-04-11 18:22:48
excerpt: 介绍初次创建 npm package 的步骤
categories: English Reading
index_img: /assets/npm-first.png
banner_img: /assets/npm-first.png
tags: npm
updated: 2019-04-11 18:22:48
---

> 原文：<https://www.danvega.dev/blog/2019/02/10/creating-your-first-npm-package/>

本文介绍了初次创建名为 wrap-with-poo 的 npm package 的步骤：

1. 创建一个目录，在该目录下打开终端输入 `npm init`，并回答一些问题，如下

   ```json
   package name: (wrap-with-poo)
   version: (1.0.0) 0.0.1
   description: This package will take any string you give it and wrap it with the poop emjoi
   entry point: (index.js)
   test command:
   git repository:
   keywords: node,npm
   author: Dan Vega
   license: (ISC) MIT
   About to write to /Users/vega/dev/npm/wrap-with-poop/package.json:
   
   {
     "name": "wrap-with-poo",
     "version": "0.0.1",
     "description": "This package will take any string you give it and wrap it with the poop emjoi",
     "main": "index.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1"
     },
     "keywords": [
       "node",
       "npm"
     ],
     "author": "Dan Vega",
     "license": "MIT"
   }
   
   
   Is this OK? (yes) yes
   ```

2. 创建入口文件 index.js，编写代码并导出

    ```js
   module.exports = str => {
     return `💩${str}💩`;
   };
   ```

3. 在另一个项目中导入这段代码有三种方法

   - 本地导入

     ```js
     npm install /Users/vega/dev/npm/wrap-with-poo
     ```

   - 使用 link

     ```js
     // wrap-with-poo 下
     npm link
     // 使用时，另一个项目下链接到 wrap-with-poo
     npm link wrap-with-poo
     ```

     原理是：`npm link` 使得项目被链接到本地 npm global folder：

     **/Users/vega/.nvm/versions/node/v10.15.0/lib/node_modules/wrap-with-poo -> /Users/vega/dev/npm/wrap-with-poo**

     `npm link wrap-with-poo` 会去本地 npm global folder 中寻找相同名字的包：

     **/Users/vega/dev/npm/wrap-with-poo-testing/node_modules/wrap-with-poo -> /Users/vega/.nvm/versions/node/v10.15.0/lib/node_modules/wra p-with-poo -> /Users/vega/dev/npm/wrap-with-poo**

   - ==发布到 npm== 

     ```bash
     # 上传到 github
     git init
     git add .
     git commit -m "Initial commit"
     git remote add origin https://github.com/cfaddict/wrap-with-poo.git
     git push -u origin master
     ```

     ```json
     // 在 package.json 中添加 homepage
     // 此时也注意一下 version (本次上传 npm 的 version 必须不同于上次)
     {
       "version": "0.0.1",
       ...
       "license": "MIT",
       "homepage": "https://github.com/cfaddict/wrap-with-poo"
     }
     ```

     ```bash
     # 上传到 npm 
     # 在这之前需要创建一个 npm 账号 https://www.npmjs.com/

     # 更换为官方镜像
     npm config set registry=http://registry.npmjs.org 
     # 第一次上传时要添加账号
     npm adduser
     # 检查/查看版本信息
     npm version
     # 上传
     npm publish
     # 换回镜像
     npm config set registry=https://registry.npm.taobao.org/
     ```

     至此，可以在任何包含 package.json 的项目中使用 `npm install wrap-with-poo` 来引入包。

另外，README.md 会显示在 npm package 的页面中，可以参考以下问题来编写 README.md：

- What does your npm package do?
- Why did you create it.
- How do you install it?
- Are there any configuration options?