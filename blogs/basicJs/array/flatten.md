---
title: 面试考点-数组扁平化
date: 2020-02-13
tags:
 - 数组
 - 扁平化
 - 面试
categories:
 - Js基础
---
### 数组扁平化

1. `replace`替换左右括号,然后`JSON.parse`方法构造数组对象(只能够打平所有维度)

   ```javascript
   function flat(arr){
        arr +=''
       let str = arr.replace(/(\[|\])/g,'')
       return JSON.parse('['+str+']')
   }
   ```

   

2. 递归去重(比较经典的扁平化方案)

   ```javascript
   function flat(arr){
       for(let key of arr){
           if(Array.isArray(key)){
               flat(key)
           }else{
               res.push(key)
           }
       }
       return res
   }
   ```

3. `replace`去除括号,`split`去掉逗号,并且将字符转换成数组

   ```javascript
   function flat(arr){
       arr += ''
       return  arr.replace(/(\[|\])/g,'').split(',')
   }
   ```

4. 通过`reduce`递归拼接

   ```javascript
   function flat(arr){
       return arr.reduce((pre,cur)=>{
          return pre.concat(Array.isArray(cur)?flat(cur):cur) 
       },[])
   }
   ```

5. `toString` 方法和`split`方法相结合

   ```javascript
   function flat(arr){
       return arr.toString().split(",").map(item => +item)//+item 将字符转换为数字
   }
   ```

6. `rest`运算符

   ```javascript
   function flat(arr){
       while(arr.some(item=>Array.isArray(item))){
           arr = [].contact(...arr)
       }
       return arr
   }
   ```

   