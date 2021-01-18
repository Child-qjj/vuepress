---
title: Vue源码(从Vue实例化开始)
date: 2021-01-12
tags:
 - 源码
categories:
 - Vue
---
## Vue源码阅读

#### 1、从new Vue()开始

###### 1.1 Vue的function实例

- **step1:** 	判断当前环境,` process.env.NODE_ENV !== 'production' &&  !(this instanceof Vue)`即警告
- **step2:** 调用init.js中定义的_init方法(跳转到initMixin实例中)

###### 1.2 initMinxin实例

- **step1:** 	定义_init方法,并挂载到Vue实例的原型上

  - **_init方法详情**	

    - 给vm赋值this(指向Vue实例对象),给vm添加_uid属性(全局下uid++)

    - `process.env.NODE_ENV !== 'production' && config.performance && mark`加权限,并且标记

    - options合并

    - 非生产环境下初始化代理(initProxy),否则定义_renderProxy方法赋值vm

    - vm._self = vm顺序调用其他初始化函数

    - ```javascript
        	initLifecycle(vm)//生命周期初始化
          initEvents(vm)// 事件初始化
          initRender(vm)// render函数初始化
          callHook(vm, 'beforeCreate')
          initInjections(vm) // resolve injections before data/props
          initState(vm)
          initProvide(vm) // resolve provide after data/props
          callHook(vm, 'created')
      //不具体看下面的代码了   
      if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
            vm._name = formatComponentName(vm, false)
            mark(endTag)
            measure(`vue ${vm._name} init`, startTag, endTag)
          }
      
          if (vm.$options.el) {
            vm.$mount(vm.$options.el)
          }
      ```

  - 

