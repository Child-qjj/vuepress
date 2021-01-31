---
title: 防抖和节流
date: 2021-01-25
tags:
 - 面试
categories:
 - Js基础
---
### 防抖

>  防止重复触发,在一定时间内的二次事件不被响应,一定要在固定时间之后才能执行下一次响应时间,第二次响应时间会因为重复触发而重置

```javascript
function debounce(fn, timespan){
    let timer = null
    return function(...args){
        clearTimeout(timer)
        timer = setTimeout(()=>{
            fn.call(this)
        },timespan)
    }
}
```

### 节流

> 防止重复触发,在一定时间内只有一次事件响应,相当于控制了一个时间段内可触发的次数,像水流一样只要一直触发事件,在一个给定时间内就必定会有一次响应

```javascript
function limit(fn, timespan) {
  let pending = false;
  return (...args: any[]) => {
    if (pending) return;
    pending = true;
    fn(...args);
    setTimeout(() => {
      pending = false;
    }, timespan);
  };
}
```

### 通过防抖节流理解闭包

> 闭包不会被释放,通常用闭包来存储中间数据,因此如节流函数,`pengding`只进行一次定义,`limit`函数不被释放,闭包的释放需要使得引用他的值不再引用来达到

```javascript
function add(){
    let num = 0;
    return ()=>{
        num++;
        console.log(num)
    }
}
let fn = add()
fn()//1
fn()//2
fn()//3
fn =null
fn = add()
fn()//1
fn()//2
fn()//3
```

