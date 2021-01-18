---
title: Css垂直居中
date: 2021-01-13
tags:
 - Css
categories:
 - Css样式
---
## 居中方法

```javascript
     1.   
          display: flex;
          align-items: center;
          justify-content: center;


​     2. 
          table; (parent)
​          display: table-cell (children);
​          vertical-align: middle;


​     3. 
          position: relative/absolute;
​          left: 50%;
​          top: 50%;
​          transform: translate( -50%, -50% )


​     4.
          position: relative;(parent)
     ​     position: absolute;(children)
     ​     left: 25%;
     ​     top: 25%;
     ​     margin-left: -该元素自身宽度的一半px;  /*水平居中*/
     ​     margin-top: -该元素自身高度的一半px;  /*垂直居中*/


​     5. 
          position: relative;(parent)
​          position: absolute;(children)
​          left: 0;
​          right: 0;   /*水平居中*/
​          top: 0;
​          background-color: burlywood;
​          bottom: 0;   /*垂直居中*/
​          margin: auto;

```



​      
