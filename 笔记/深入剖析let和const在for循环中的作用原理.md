# 深入剖析let/const在for循环中的作用原理
```js
var a=[];  
for(var i = 0;i<3;i++){   
   a[i]=function(){console.log(i)}  
}
a[1](); //3
```
for循环执行过程伪代码如下：

```js
var a=[]
var i = 0;

if(i<3) {
  a[i]=function(){console.log(i)}  
}
i++;

if(i<3) {
  a[i]=function(){console.log(i)}  
}
i++;

if(i<3) {
  a[i]=function(){console.log(i)}  
}
i++;

//a ==> [function(){console.log(i), function(){console.log(i), function(){console.log(i)}]

//a[1] ==> function(){console.log(i)

a[1](); // console.log(i) ==> 3 因为 i 现在的值是3

```
console.log(i) ==> 3 因为 i 现在的值是3！这句话不对！
console.log(i) ==> 3 因为 i 现在的值是3！这句话不对！
console.log(i) ==> 3 因为 i 现在的值是3！这句话不对！

而是块级作用域内并没有i，要去上一级作用域局部作用域中找，找到i，局部作用域中分 i现在的值是3。

如果把for循环的var换成let呢？

```js
var a=[];  
for(var i = 0;i<3;i++){   
   a[i]=function(){console.log(i)}  
}
a[1](); //1
```
console.log(i)在其块级作用域内就找到了i，每次for循环把当前i值存入块级作用域。所以a[1]()会打印1；

