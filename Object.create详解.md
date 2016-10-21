# Object.create
简单来讲，new Object()是一种通过构造函数来创建object的方式，而Object.create(proto, [ propertiesObject ])
不需要通过构造函数就可以创建一个object，Object.create()的第一个参数是必须要的，第二个参数可选。其实Object.create()内部依然是通过new一个构造函数的方式来实现的，它有构造函数，不过这个构造函数是隐式存在的，看一下使老旧浏览器支持Object.create方法的“polyfill”就可以对它们之间的区别一目了然了：

```js
if (!Object.create) {
    Object.create = function (o) {
    function F() {}  //定义了一个隐式的构造函数
    F.prototype = o;
    return new F();  //其实还是通过new来实现的
    };
  }
 ```
ps:Object.create可以用于对象的深拷贝，非常好用
