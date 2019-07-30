---
title: 面试题
lang: zh-CN
description: 用于积累面试题。
meta:
  - name: keywords
    content: javascript, 面试题
---

## JavaScript 的事件循环 ##

```js
//请写出输出内容
async function async1() {
  console.log('async1 start');
  await async2();
  console.log('async1 end');
}
async function async2() {
  console.log('async2');
}

console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0)

async1();

new Promise(function(resolve) {
  console.log('promise1');
  resolve();
}).then(function() {
  console.log('promise2');
});
console.log('script end');
```

具体分析看 <https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/7> 。

## 前端中的模块化开发 ##

<https://www.processon.com/view/link/5c8409bbe4b02b2ce492286a#map>

## Vue 的父组件和子组件生命周期钩子执行顺序是什么 ##

1. 加载渲染过程
    1. 父组件： `beforeCreate` -> `created` -> `beforeMount`
    2. 子组件： -> `beforeCreate` -> `created` -> `beforeMount` -> `mounted`
    3. 父组件： -> `mounted`

2. 子组件更新过程

    1. 父组件： `beforeUpdate`
    2. 子组件： -> `beforeUpdate` -> `updated`
    3. 父组件： -> `updated`

3. 父组件更新过程

    1. 父组件： `beforeUpdate` -> `updated`

4. 销毁过程

    1. 父组件： `beforeDestroy`
    2. 子组件： -> `beforeDestroy` -> `destroyed`
    3. 父组件： -> `destroyed`

## 介绍下 npm 模块安装机制，为什么输入 `npm install` 就可以自动安装对应的模块 ##

npm 模块安装机制：

* 发出npm install命令
* 查询node_modules目录之中是否已经存在指定模块
  * 若存在，不再重新安装
  * 若不存在
    * npm 向 registry 查询模块压缩包的网址
    * 下载压缩包，存放在根目录下的.npm目录里
    * 解压压缩包到当前项目的node_modules目录

更详细的请查看： <https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/22> 。

## kebab-case字符串与camelCase字符串互相转换 ##

以下全部以`'kebab-case'`、`'kebabCase'`为例

### `'kebab-case'`转`'kebabCase'` ###

#### `slice` ####

1. 第一种

``` javascript
function strToCamelCase(str){
    let res = str.split('-').map(element => element.slice(0, 1).toUpperCase() + element.slice(1)).join('');
    return res.slice(0, 1).toLocaleLowerCase() + res.slice(1);
}

strToCamelCase('kebab-case'); // "kebabCase"
```

2. 第二种

``` javascript
function strToCamelCase(str){
    let res = str.split('-').map(element => `${element.slice(0, 1).toUpperCase()}${element.slice(1)}`).join('');
    return `${res.slice(0, 1).toLocaleLowerCase()}${res.slice(1)}`;
}

strToCamelCase('kebab-case');  // "kebabCase"
```

3. 第三种

``` javascript
function strToCamelCase(str){
    return `${str.slice(0, 1)}${str.slice(1).replace(/-([a-z])/g, (match, p1, offset, string) => { return p1.toUpperCase()})}`
}

strToCamelCase('kebab-case');  // "kebabCase"
```

#### `charAt` 与 `slice` ####

1. 第一种

``` javascript
function strToCamelCase(str){
    return str.split('-').map(element => element.charAt(0).toUpperCase() + element.slice(1)).join('');
}

strToCamelCase('kebab-case'); // "kebabCase"
```

2. 第二种

``` javascript
function strToCamelCase(str){
    return `${str.charAt(0)}${str.slice(1).replace(/-([a-z])/g, (match, p1, offset, string) => { return p1.toUpperCase()})}`
}

strToCamelCase('kebab-case'); // "kebabCase"
```

#### `substring` ####

``` javascript
function strToCamelCase(str){
    return str.split('-').map(element => element.substring(0, 1).toUpperCase() + element.substring(1)).join('');
}

strToCamelCase('kebab-case'); // "kebabCase"
```

#### `substr` ####

``` javascript
function strToCamelCase(str){
    return str.split('-').map(element => element.substr(0, 1).toUpperCase() + element.substr(1)).join('');
}

strToCamelCase('kebab-case'); // "kebabCase"
```

### `'kebabCase'`转`'kebab-case'` ###

``` javascript
function strToKebabCase(str){
    return str.replace(/([A-Z])/g, (match, p1, offset, string) => { return `-${p1.toLowerCase()}` })
}

strToKebabCase('kebabCase'); // "kebab-case"
```

## 时间格式化 ##

时间格式转换，第一个参数接受的时间格式字符串，第二个参数可以传入一个时间点或者不传（当前时间）。

``` javascript
function dateFormat(format, date){
    let addZero = (num) => { return num < 10 ? `0${num}` : num },
        now = date ? new Date(date.replace(/-/g, '/')) : new Date(),
        res = format;
    if(format.indexOf('YYYY') > -1) res = res.replace('YYYY', now.getFullYear())
    if(format.indexOf('MM') > -1) res = res.replace('MM', addZero(now.getMonth() + 1))
    if(format.indexOf('DD') > -1) res = res.replace('DD', addZero(now.getDate()))
    if(format.indexOf('hh') > -1) res = res.replace('hh', addZero(now.getHours()))
    if(format.indexOf('mm') > -1) res = res.replace('mm', addZero(now.getMinutes()))
    if(format.indexOf('ss') > -1) res = res.replace('ss', addZero(now.getSeconds()))
    if(format.indexOf('day') > -1) res = res.replace('day', now.getDay())
    return res;
}

dateFormat('YYYY'); // '2019'
dateFormat('YYYY-MM'); // '2019-03'
dateFormat('YYYY-MM-DD'); // '2019-03-10'
dateFormat('YYYY-MM-DD hh:mm:ss'); // '2019-03-10 15:03:10'
dateFormat('YYYY-MM-DD hh:mm:ss day'); // '2019-03-10 15:03:10 0'
dateFormat('YYYY-MM-DD hh:mm:ss day', '2019-3-8'); // '2019-03-08 00:00:00 5'
```

## this ##

``` javascript
a = 5;

function foo(){
    console.log(this.a);
}

let obj = {
    a: 2,
    foo: foo
}

let bar = obj.foo;

foo()
obj.foo()
bar()
```

上面console依次打印的结果： 5  2  5。

具体原因可以查看此处：[https://github.com/yygmind/blog/issues/20](https://github.com/yygmind/blog/issues/20) 。

## 闭包 ##

页面中有5个按钮，依次为每个按钮绑定事件，点击按钮打印出它是第几个按钮，代码如下：

``` javascript
var buttons = document.getElementsByTagName('button');
for(var i = 0; i < buttons.length; i++) {
    buttons[i].onclick = function() {
        console.log(i);
    }
}
```

请问按钮点击输出的内容是什么？你对这段代码怎么优化？

**答案：**每个按钮点击输出的都是 5 。
对于此段代码的优化：

``` javascript
var buttons = document.getElementsByTagName('button');
for(var i = 0, len = buttons.length; i < len; i++) {
    (function(i){
        buttons[i].onclick = function() {
            console.log(i);
        }
    })(i);
}
```

或者是全部使用let。

``` javascript
let buttons = document.getElementsByTagName('button');
for(let i = 0, len = buttons.length; i < len; i++) {
    buttons[i].onclick = function() {
        console.log(i);
    }
}
```

上面这个题让我想到了之前看到的一道题，请问下面代码输出的内容是：

``` javascript
for(var i = 0; i < 5; i++) {
    setTimeout(function(){
        console.log(i)
    }, 0);
}
```

输出的都是 5 。那怎么可以延迟输出0,1,2,3,4呢？

可以这样：

``` javascript
for(var i = 0; i < 5; i++) {
    (function(i){
        setTimeout(function(){
            console.log(i)
        }, 0);
    })(i)
}
```

或者：

``` javascript
for(let i = 0; i < 5; i++) {
    setTimeout(function(){
        console.log(i)
    }, 0);
}
```
