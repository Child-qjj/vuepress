---
title: Vue源码(Watcher和Dep)
date: 2021-01-16
tags:
 - 源码
categories:
 - Vue
---
## `Vue`源码阅读之Watcher(观察者)构成详解

> 个人建议,`vue`的源码阅读初学者直接看dist文件夹下的`vue.runtime.esm.js`这里包含了你想看的函数实例,最主要的是**方便搜索**,**方便搜索**,**方便搜索**,在开发包中你能看到无限个函数调用,无限套娃,打击学习兴趣(极其)

### 管理Watcher 的工具类 -----`Dep`(基于发布订阅模式)

###### `Dep`中传入的参数

1. `target` ---> 管理的Watcher实例
2. `id`---> 临时生成的唯一id值
3. `subs` ---> 观察队列

`Dep`原型上挂载的方法

1. `addSub`,添加`watcher`实例对象(被观察的对象)到`subs队列`

2. `removeSub`,将`watcher`实例对象(被观察的对象)从`subs队列`中移除

3. `depend`

   - 在`getter`中被调用
   - 如果`Dep.tartget`存在,添加实例对象到`subs队列`

4. `notify`

   - 在`Setter`中被调用

   - `var subs = this.subs.slice()`,通过`slice`方法,添加数组方法
   - 不是生产环境,且`config.async`为`false`,调用数组中的`sort()方法`对`subs队列`排序
   - 遍历`subs队列`,调用`Watcher`中的`update`方法,通知更新

### Watcher中传入的参数

1. `vm` ---> `Vue` 的实例化对象
2. `expOrFn` ---> 赋值给`this.getter`,如果`typeof`是函数类型直接赋值,不是就通过`parsePath`函数转换后赋值
3. `cb`---> 传入回调函数,`noop`无任何操作,空函数体
4. `options`-->`Watcher函数的配置信息,如果lazy属性不存在或者false,会调用Watcher原型上的get方法`
5. `isRenderWatcher `-->是否渲染`Watcher`

### Watcher原型上挂载的方法

```javascript
// 公共方法
var targetStack = [];

function pushTarget (target) {
  targetStack.push(target);
  Dep.target = target;
}

function popTarget () {
  targetStack.pop();
  Dep.target = targetStack[targetStack.length - 1];
}
```



1. `Watcher.prototype.get`
   - 首先压入临时任务栈中,因为同时只能有一个观察者被`evaluated`(评估?执行?),所以`target`是全局唯一的
   - `try catch`函数首先给value赋值为`this.getter`函数的返回值,然后收集依赖,出栈,清除依赖
2. `Watcher.prototype.addDep`
   - 添加依赖项(在`Dep`中被调用)
3. `Watcher.prototype.cleanupDeps`
   - 清楚当前观察对象的所有依赖搜集
4. `Watcher.prototype.update`
   - 在依赖改变时调用,`lazy`状态会进行脏标记,`sync`状态会执行`run`方法,否则推送到观察者队列中
5. `Watcher.prototype.run`
   - 在值相同,但深度或类型不同的情况下启动深度观察
6. `Watcher.prototype.evaluate`
   - 仅被`lazy Watchers`(Watcher实例的lazy属性为true)调用,调用get方法,更新依赖,清楚脏标记
7. `Watcher.prototype.depend`
   - 依赖所有依赖?
8. `Watcher.prototype.teardown`
   - 在实例销毁的情况下移除Sub中的该`vm`实例


```javascript
 */
var Watcher = function Watcher (
  vm,
  expOrFn,
  cb,
  options,
  isRenderWatcher
) {
  //console.log('vm',vm);
  //console.log('expOrFn',expOrFn);
  console.log('cb',cb);
  console.log('options',options);
  // console.log('isRenderWatcher',isRenderWatcher);
  this.vm = vm;
  if (isRenderWatcher) {
    vm._watcher = this;
  }
  vm._watchers.push(this);
  // options
  if (options) {
    this.deep = !!options.deep;
    this.user = !!options.user;
    this.lazy = !!options.lazy;
    this.sync = !!options.sync;
    this.before = options.before;
  } else {
    this.deep = this.user = this.lazy = this.sync = false;
  }
  this.cb = cb;
  this.id = ++uid$2; // uid for batching
  this.active = true;
  this.dirty = this.lazy; // for lazy watchers
  this.deps = [];
  this.newDeps = [];
  this.depIds = new _Set();
  this.newDepIds = new _Set();
  this.expression = process.env.NODE_ENV !== 'production'
    ? expOrFn.toString()
    : '';
  // parse expression for getter
  if (typeof expOrFn === 'function') {
    this.getter = expOrFn;
  } else {
    this.getter = parsePath(expOrFn);
    if (!this.getter) {
      this.getter = noop;
      process.env.NODE_ENV !== 'production' && warn(
        "Failed watching path: \"" + expOrFn + "\" " +
        'Watcher only accepts simple dot-delimited paths. ' +
        'For full control, use a function instead.',
        vm
      );
    }
  }
  this.value = this.lazy
    ? undefined
    : this.get();
};

/**
 * Evaluate the getter, and re-collect dependencies.
 */
Watcher.prototype.get = function get () {
  pushTarget(this);
  var value;
  var vm = this.vm;
  try {
    value = this.getter.call(vm, vm);
  } catch (e) {
    if (this.user) {
      handleError(e, vm, ("getter for watcher \"" + (this.expression) + "\""));
    } else {
      throw e
    }
  } finally {
    // "touch" every property so they are all tracked as
    // dependencies for deep watching
    if (this.deep) {
      traverse(value);
    }
    popTarget();
    this.cleanupDeps();
  }
  return value
};

/**
 * Add a dependency to this directive.
 */
Watcher.prototype.addDep = function addDep (dep) {
  var id = dep.id;
  if (!this.newDepIds.has(id)) {
    this.newDepIds.add(id);
    this.newDeps.push(dep);
    if (!this.depIds.has(id)) {
      dep.addSub(this);
    }
  }
};

/**
 * Clean up for dependency collection.
 */
Watcher.prototype.cleanupDeps = function cleanupDeps () {
  var i = this.deps.length;
  while (i--) {
    var dep = this.deps[i];
    if (!this.newDepIds.has(dep.id)) {
      dep.removeSub(this);
    }
  }
  var tmp = this.depIds;
  this.depIds = this.newDepIds;
  this.newDepIds = tmp;
  this.newDepIds.clear();
  tmp = this.deps;
  this.deps = this.newDeps;
  this.newDeps = tmp;
  this.newDeps.length = 0;
};

/**
 * Subscriber interface.
 * Will be called when a dependency changes.
 */
Watcher.prototype.update = function update () {
  /* istanbul ignore else */
  if (this.lazy) {
    this.dirty = true;
  } else if (this.sync) {
    this.run();
  } else {
    queueWatcher(this);
  }
};

/**
 * Scheduler job interface.
 * Will be called by the scheduler.
 */
Watcher.prototype.run = function run () {
  if (this.active) {
    var value = this.get();
    if (
      value !== this.value ||
      // Deep watchers and watchers on Object/Arrays should fire even
      // when the value is the same, because the value may
      // have mutated.
      isObject(value) ||
      this.deep
    ) {
      // set new value
      var oldValue = this.value;
      this.value = value;
      if (this.user) {
        try {
          this.cb.call(this.vm, value, oldValue);
        } catch (e) {
          handleError(e, this.vm, ("callback for watcher \"" + (this.expression) + "\""));
        }
      } else {
        this.cb.call(this.vm, value, oldValue);
      }
    }
  }
};

/**
 * Evaluate the value of the watcher.
 * This only gets called for lazy watchers.
 */
Watcher.prototype.evaluate = function evaluate () {
  this.value = this.get();
  this.dirty = false;
};

/**
 * Depend on all deps collected by this watcher.
 */
Watcher.prototype.depend = function depend () {
  var i = this.deps.length;
  while (i--) {
    this.deps[i].depend();
  }
};

/**
 * Remove self from all dependencies' subscriber list.
 */
Watcher.prototype.teardown = function teardown () {
  if (this.active) {
    // remove self from vm's watcher list
    // this is a somewhat expensive operation so we skip it
    // if the vm is being destroyed.
    if (!this.vm._isBeingDestroyed) {
      remove(this.vm._watchers, this);
    }
    var i = this.deps.length;
    while (i--) {
      this.deps[i].removeSub(this);
    }
    this.active = false;
  }
};

```

