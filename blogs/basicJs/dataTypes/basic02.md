# 2.JS类型判断和转换

> javascript 属于弱语言类型,不需要对变量的类型进行声明,它会根据环境自动进行转换,变量的类型由它的值决定.也就是说,可以给一个变量赋予多个类型的值(不推荐),这样一来,我们在生产过程中就不能确定变量的确切类型,因此变量的类型判断至关重要:(本文的高级程序设计页码均为PDF格式的页码)
>
> 《你不知道的JavaScript(中)》: JavaScript中的变量是没有类型的，只有值才有。变量可以随时持有任何类型的值;

```javascript
let a = [];
a=9;
a = {}
console.log(a);// a = {};
```



## 类型判断的方法

1. typeof(《JavaScript权威指南》中对 typeof 的介绍：typeof 是一元操作符，放在其单个操作数的前面，操作数可以是任意类型。返回值为表示操作数类型的一个字符串。)

   - typeof 方法能够检测数据的类型,返回值就该值的数据类型(返回值小写&&字符串)

   - typeof 方法具有局限性,只能判断出一些基本数据类型的值,对应的返回值如下表(见下表可知,typeof不能准确的判断Null和Object的类型):

     | 数据类型  | 返回值      |
     | --------- | ----------- |
     | String    | “string”    |
     | Null      | “object”    |
     | Undefined | “undefined” |
     | Number    | “number”    |
     | Object    | “object”    |
     | symbol    | "symbol"    |
     | Boolean   | “boolean”   |
     | Function  | “function”  |

     >  **虽然Null数据类型返回值是一个”object”,但是Null数据类型并不属于对象类型,在红宝书上是这么描述的(参考:《JavaScript高级程序设计(第四版)》P 32):**
     >
     > ​		**逻辑上讲，null 值表示一个空对象指针，这也是给 typeof 传一个 null 会返回"object"的原因**

2. instanceof 

   - `《JavaScript高级程序设计(第四版)》:instanceof 操作符可以用来确定一个对象 实例的原型链上是否有原型`,返回一个boolean类型的值

   - 如果说typeof适用于判断基本数据类型,那么instaceof试用于判断对象类型,不过instanceof的判断不够直观, 理论上来说所有复杂对象类型的原型上都有Object原型

     ```javascript
     let fn = function(){}//值得注意的是函数的原型链上既有Function原型方法又有Object原型方法
     let arr = [];
     let obj = {
       name:"sd"
     }
     console.log(fn instanceof Function); //true
     console.log(fn instanceof Object);    //true
     console.log(arr instanceof Array);//true
     console.log(arr instanceof Object);//true
     console.log(obj instanceof Object);//true
     console.log(obj instanceof Function);//false
     ```

   - 那么instanceof 是否能判断基本数据类型呢?(答案是:能)------>>>具体参见[神三元的博客](http://47.98.159.95/my_blog/js-base/002.html#_2-instanceof%E8%83%BD%E5%90%A6%E5%88%A4%E6%96%AD%E5%9F%BA%E6%9C%AC%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%EF%BC%9F)

   - instanceof 功能的实现

     ```javascript
     function instanceOf(A,B){//instanceof 原理就是沿着对象的原型链一直
         let P = A;
         while(P.__proto__){	//查找,直到找到原型方法或者找不到返回布尔值;
           if(P === B.prototype) return true; 
            P = P.__proto__;
        }
       return false;
     }
     ```

3. typeof和instanceof方法都有其局限性,在JS的内置方法里面就有一个方法,能够完美的判断出数据的类型(强烈推荐使用该方ya法):Object.prototype.toString()方法,返回对象的字符串描述,格式为“[Object,object]”(详见《JavaScript高级程序设计(第四版)》:`P55`)

   1. 详细封装可见[冴羽的github](https://github.com/mqyqingfeng/Blog/issues/28)

   - ```javascript
     function checkType(val){//看完博客不久之后自己封装的函数,大体参照冴羽的函数,在博客写更多是为了加深记忆
      return Object.prototype.toString.call(val).split(" ")[1].replace(/\]/,"").toLowerCase();
     }//精确判断各种类型
     console.log(checkType([]));//array
     console.log(checkType({}));//object
     console.log(checkType(null));//null
     console.log(checkType(undefined));//undefined
     console.log(checkType(()=>{}));//function
     console.log(checkType(new Date()));//date
     console.log(checkType(/\]/));//regexp
     ```

     >  **注意事项**
     >
     > NaN === NaN //false Object.is 方法修复了或者说解决这个问题
     >
     > a !== a 那么a一定是NaN
     >
     > 这就是Object.is 和 === 等的区别;
     >
     > 在日常项目种更多的使用typeof 方法和=== 结合来判断大部分的数据类型

## 类型转换(晕头转向)

> **对于类型转换来说,先列举特殊的例子 **
>
> 对象类型的数据转化为字符串都是“[Object,object]”(这里的对象仅表示普通对象{...})
>
> 其它类型转化为number类型(仅有返回值符合以下要求的能转化成数字,其他为NaN)
>
> 1.连续的数字字符串;2.数组为空或者数组仅有一个元素,且该元素为数字字符串或数字;3.布尔类型;

1. 转布尔类型:
   - 空字符串,0,-0,NaN,undefined,null 为false,其余为true
2. 转字符string类型
   - 数组 [1,2]=>‘1,2’
   - 布尔/函数/symbol
3. 转数字number类型(函数或对象返回值满足也可)
   - 连续的数字字符串;
   - 数组为空或者数组仅有一个元素,且该元素为数字字符串或数字;
   - 布尔类型;
   - 除数组之外的引用类型NaN
   - symbol =>err

