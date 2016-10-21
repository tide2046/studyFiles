半年前曾经写过一篇[Koa技术分享](https://cnodejs.org/topic/56936889c2289f51658f0926)，当时写这篇文章的时候还在校园，实习期间接触一个Koa的项目，之后自己又写了一个[小项目](https://github.com/a1511870876/myblog)，之后总结心得便有了上面这篇文章。半年的时间，忙于毕业和论文，git和文章都鲜有更新，如今毕业就业，再次接触Koa，再看这篇文章，感到当时的稚嫩，有很多地方没有考虑仔细，理解也不深入。怀着程序员严谨的精神，准备重写一篇Koa的技术分享。

tj的thunkify源码
```
/**
 * Module dependencies.
 */
var assert = require('assert');
/**
 * Expose `thunkify()`.
 */
module.exports = thunkify;
/**
 * Wrap a regular callback `fn` as a thunk.
 *
 * @param {Function} fn
 * @return {Function}
 * @api public
 */
function thunkify(fn){
  assert('function' == typeof fn, 'function required');
  return function(){
    //这里就是将所有的参数放进了一个新的数组，这里之所以不用[].slice。是因为有人在bluebird docs发现，如果直接这样泄露arguments，v8的一些优化的编译会被搁置，就会有性能上的损失。
    var args = new Array(arguments.length);
    var ctx = this;
    for(var i = 0; i < args.length; ++i) {
      args[i] = arguments[i];
    }
    return function(done){
      //这里用called是为了标记只执行了一次，类似于promise的resolve和reject只能执行一次一样。
      var called;
      args.push(function(){
        if (called) return;
        called = true;
        //因为arguments是一个list，必须得用apply才能在done传入。
        done.apply(null, arguments);
      });
      //这里用个try catch，可以在执行失败时走一遍callback，传入err信息
      try {
        fn.apply(ctx, args);
      } catch (err) {
        done(err);
      }
    }
  }
};
```
下面的是co源码的逐行阅读，先把参照的一些图片列举出来
```  
//array原生的slice
  var slice = Array.prototype.slice;
  //这里写的这么古怪就只是想在es6的模块引入时更加舒服一些，参见下面的图片3
  module.exports = co['default'] = co.co = co;
  //将传入的generator函数包装成一个返回promise的方法
  //这是一个独立的方法，就是将传入的函数包装成了co执行前的形式
  co.wrap = function (fn) {
    //存了一个指针指向原generator函数
    createPromise.__generatorFunction__ = fn;
    return createPromise;
    function createPromise() {
      //返回的方法调用就会直接执行co。
      return co.call(this, fn.apply(this, arguments));
    }
  };
  //执行generator或者generator函数然后返回一个promise
  function co(gen) {
    var ctx = this;
    var args = slice.call(arguments, 1)
    // 将所有的东西放到一个promise里面，来防止引起内存泄露错误的promise chaining。
    //tudo：看一下这个issue see https://github.com/tj/co/issues/180
    //参见下面的内存泄露的研究
    //https://github.com/promises-aplus/promises-spec/issues/179 看的我好累，完全没有看懂啊！！！
    //总之不管怎样，他是把传进来的东西包装成了一个promise
    return new Promise(function(resolve, reject) {
      //这里是判断下gen是不是函数，generators function执行之后是一个object
      if (typeof gen === 'function') gen = gen.apply(ctx, args);
      //传入的不是generators函数，没有next，就直接resolve返回结果;这里是错误兼容而已，因为co就是基于generator的，传入其他的没有意义
      if (!gen || typeof gen.next !== 'function') return resolve(gen);
      //主要就是走下面的onFulfilled方法，这个方法返回的是一个promise(resolve或者reject)
      onFulfilled();
      function onFulfilled(res) {
        var ret;
        try {
          //调用第一次next方法
          ret = gen.next(res);
        } catch (e) {
          //出错了直接reject出去
          return reject(e);
        }
        //将第一次的结果({done:true,value:{}})传入内部方法next
        next(ret);
      }
      //promise失败的时候调用
      //这里在promise错误的时候，就会尝试向外throw err。Genertor的属性，可以内部抛出，外部不活。如果我们对这个yield进行了try catch，就会被捕获，不处理的话，就会reject出去，在co的catch语句中co(*fn).catch处理。
      function onRejected(err) {
        var ret;
        try {
          ret = gen.throw(err);
        } catch (e) {
          return reject(e);
        }
        next(ret);
      }
      //循环得到next的结果，return的还是一个promise
      function next(ret) {
        //如果done为true的话，代表执行结束，返回一个resolve的promise
        if (ret.done) return resolve(ret.value);
        //既然还没执行完，就将ret.value转换成一个promise
        var value = toPromise.call(ctx, ret.value);
        //如果成功转化为了promise，就在这个promise执行完了再调用onFulfilled方法
        if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
        return onRejected(new TypeError('You may only yield a function, promise, generator, array, or object, '
          + 'but the following object was passed: "' + String(ret.value) + '"'));
      }
    });
  }
  //将yield后面的东西转化成一个promise
  function toPromise(obj) {
    //如果不存在的话，直接返回，走最后的报错流程
    if (!obj) return obj;
    //判断传入的是不是promise，是的话直接返回
    if (isPromise(obj)) return obj;
    //判断传入的是不是generator，或者generator function，是的话，继续调用co函数进行循环~
    if (isGeneratorFunction(obj) || isGenerator(obj)) return co.call(this, obj);
    //如果就是个普通的thunk函数，也把他转化为promise
    if ('function' == typeof obj) return thunkToPromise.call(this, obj);
    //如果是array或者object的话，也走相应地变换方法
    if (Array.isArray(obj)) return arrayToPromise.call(this, obj);
    if (isObject(obj)) return objectToPromise.call(this, obj);
    //如果都不是，直接返回，走最后的报错流程
    return obj;
  }
  //这里将thunk转化成了promise，thunk就是调用的时候传入一个error和res的function，就在最外面包了个promise就行了
  function thunkToPromise(fn) {
    var ctx = this;
    return new Promise(function (resolve, reject) {
      fn.call(ctx, function (err, res) {
        if (err) return reject(err);
        if (arguments.length > 2) res = slice.call(arguments, 1);
        resolve(res);
      });
    });
  }
  //这里的array转化为promise其实就是通过Promise.all来包裹，这个方法只接受promise的数组，并且装化为一个新的promise
  //参见下面的promise平行执行的研究
  function arrayToPromise(obj) {
    return Promise.all(obj.map(toPromise, this));
  }
  //将一个object转化为promise，其实就是内部调用了promise.all方法而已
  function objectToPromise(obj){
    var results = new obj.constructor();
    var keys = Object.keys(obj);
    var promises = [];
    for (var i = 0; i < keys.length; i++) {
      var key = keys[i];
      var promise = toPromise.call(this, obj[key]);
      if (promise && isPromise(promise)) defer(promise, key);
      else results[key] = obj[key];
    }
    return Promise.all(promises).then(function () {
      return results;
    });
    function defer(promise, key) {
      // predefine the key in the result
      results[key] = undefined;
      promises.push(promise.then(function (res) {
        results[key] = res;
      }));
    }
  }
  //检查是否是promise，果然就是简单的判断他有没有then方法
  function isPromise(obj) {
    return 'function' == typeof obj.then;
  }
  //这里判断是不是generator就是判断他的next和throw方法是不是function
  function isGenerator(obj) {
    return 'function' == typeof obj.next && 'function' == typeof obj.throw;
  }
  //判断是否是generatorFunction就是判断了他的constructor的name
  function isGeneratorFunction(obj) {
    var constructor = obj.constructor;
    //这里是为了解决没有constructor的对象，比如Object.create(null)
    if (!constructor) return false;
    //这里两种情况会返回true，一种是名字正确地，一种是他的prototype是generator
    if ('GeneratorFunction' === constructor.name || 'GeneratorFunction' === constructor.displayName) return true;
    return isGenerator(constructor.prototype);
  }
   //就是通过constructor来判断是不是一个简单的对象
  function isObject(val) {
    return Object == val.constructor;
  }
```