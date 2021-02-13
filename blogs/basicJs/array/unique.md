---
title: 面试考点-数组去重
date: 2020-02-13
tags:
 - 数组
 - 去重
 - 面试
categories:
 - Js基础
---
####  复习

###### 数组去重

1. set 去重

   ```javascript
   function unique(arr){
       return [...new Set(arr)]
   }
   ```

2. `indexof/includes等api`

   ```javascritp
   function unique(arr){
       let res = []
       for(let i=0;i<arr.length;i++){
           if(res.indexOf(key) === -1 ){
               res.push(key)
           }
       }
      return res
   }
   ```

   3.经典`for`循环

   ```javascript
   function unique(arr){
       for (let i = 0; i < arr.length; i++) {
         for (let j = i+1; j < arr.length; j++) {
             if(arr[i] === arr[j]){
                 arr.splice(j,1)
                 j--
             }
         }
       }
       return arr
   }
   ```

   4.对象无重复键的特性

   ```javascript
   function unique(arr){
               let obj = {}
               return arr.filter(function(item, index, arr){
                   return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof 						item + item] = true)
               })
           }
   ```

   