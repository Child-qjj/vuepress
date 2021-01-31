---
title: 继承方法赏析集
date: 2021-01-31
tags:
 - js
categories:
 - Js基础
---
### `Es5`继承方法赏析集	

> 面试考点,感觉在笔试出现概率大点,面试可能只是提下有几种方式,以及各个方式的优缺点,不一定会让具体去实现

###### 一、原型链继承

> 原型链继承,巧妙的利用`new`原理和原型链的相关知识,完成了继承的实现

```javascript
//原型链继承
		function Parent() {
            this.name = ['jack']
            this.age = 10
        }
        Parent.prototype.get = function(){
            console.log(this.name);
            console.log(this.age);
        }
        function Child(){

        }
        Child.prototype = new Parent()//new 发生了什么?详情见链接
        let child  = new Child()
        let childs = new Child()
        child.name.push('add')
        child.age = 20 
        console.log(childs.get());// ['jack','add'], 10
```

> [new 一个对象发生了什么]()

1. 原型链继承的优点
   - 能够访问到被继承对象的实例方法
   - 能够访问到被继承对象原型链上的方法
2. 原型链继承的缺点
   - 被继承下来的实例对象引用类型的属性被所有实例共享,任何子对象修改实例上的引用类型属性都会导致其他子对象访问到的属性改变
   - 不能够传递参数

###### 二、构造函数继承(也称为经典继承)

> 

```javascript
         function Parent(age) {
            this.name = ['jack']
            this.age = age
        }
        Parent.prototype.get = function(){
            console.log(this.name);
            console.log(this.age);
        }
        function Child(name){
            Parent.call(this,name)
        }
        
        let child = new Child(20)
        let childs = new Child()
        child.name.push('add')
         
        console.log(child.age); //20
        console.log(childs.name);// ['jack']
        console.log(childs.age);//undefined
		console.log(chidls.get()); // err
```

1. 构造函数继承的优点
   - 每个实例之间互不干扰
   - 可以传递参数
2. 构造函数继承的缺点
   - 方法都在构造函数中,没创建一个实例都创建一遍方法

###### 三、组合继承

> 组合继承,糅合了原型链继承和构造函数继承的优点,

```javascript
		function Parent(age,name) {
            this.name = name
            this.age = age
            this.color = ['red','blue','green']
        }
        Parent.prototype.get = function(){
            console.log(this.name);
            console.log(this.age);
        }
        function Child(age,name){
            Parent.apply(this,[name,age])
        }
        Child.prototype =new Parent() // 丢失了constructor
        Child.prototype.constructor = Child // 使得Child的constructor 仍然是Child本身
        let child = new Child('jack',20)
        let childs = new Child()
       // child.name.push('add')
         
        console.log(child.age); //20
        console.log(child.name)
        console.log(childs.name);// ['jack']
        console.log(childs.age);//undefined
```

四、原型式继承

> 与原型链具有相同缺点

```javascript
 		function createObj(o){
            function F(){}
            F.prototype = o
            return new F()

        }
        var person = {
            name: 'kevin',
            friends: ['daisy', 'kelly']
        }

        var person1 = createObj(person);
        var person2 = createObj(person);

        person1.name = 'person1';
        console.log(person2.name); // kevin

        person1.friends.push('taylor');
        console.log(person2.friends); // ["daisy", "kelly", "taylor"]
```

五、寄生式继承

> 与构造函数继承有相同缺点,需要重复构建函数方法

```javascript
        function createObj(o){
            let clone = Object.create(o)
            clone.ways = function(){
                console.log("There are many different ways")
            }
            return clone
        }
		var person1 = createObj(person);
        var person2 = createObj(person);

        person1.name = 'person1';
        console.log(person2.name); // kevin

        person1.friends.push('taylor');
        console.log(person2.friends); // ["daisy", "kelly", "taylor"]
```

六、寄生组合式继承

> 糅合寄生式继承和组合继承的优点,继承必考点

```javascript
		function createObj(o){
            function F(){}
            F.prototype = o
            return new F()
        }
		function extd(child,parent){
            let prototype = createObj(parent.prototype)
            prototype.constructor = child
            child.prototype = prototype 
        }
```

