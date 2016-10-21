# JavaScript中Array的一些操作
## 1、判断一个对象是否为数组
```
function isArray(a) {
  return Array.isArray ? Array.isArray(a) : Object.prototype.toString.call(a) === '[object Array]';
}
```
　　Array.isArray是ES6新增的一个方法，考虑不是所有浏览器都支持ES5，先做判断，如果浏览器支持Array.isArray()则使用，否则使用Object.prototype.toString.call()方法，注意这里说的toString()是Object原型链上的toString()方法，和obj.toString()是两码事。Object.prototype.toString.call()只会返回[Object [[Class]]]这种形式。
## 2、数组虑重
　　一共总结了四种方法，其中前面两种实际编码中不会用到，但是面试的时候可以提一下。
#### 双重循环
　　就不多说了，挨个儿比较，时间复杂度为O(n^2)。
#### 排序后遍历
　　先对数组排序，由于数组已经有序，只需要检查数组中第i个元素与新的结果数组中最后一个元素是否相等即可，如果不相等，则放到新的结果数组中，这种方法的时间复杂度为O(n)，但是还要考虑排序的消耗，所以同样不可取。这两种方法比较简单，均不上代码。
#### Hash的方法
　　散列表（Hash table，也叫哈希表），是根据关键码值(Key value)而直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表。   
　　把Hash表用于数组去重就在于，把数组的值依次存入Hash表的Key值，Value值可以存储Key出现的次数。存取的时候产生冲突的话，忽略该元素，反之把该元素存入新数组。该方法时间复杂度为O(n)，是应用最为广泛的去重方法，而在JavaScript语言中，Hash表是用Object实现的，代码如下。
```
function unique(array) {
    var newArray = [];
    var object = {};
    for (var i = 0; i < array.length; i++){
        if(!object[array[i]]) {
            newArray.push(array[i]);
            object[array[i]] = 1;
        }
    }
    return newArray;
}
```
#### Set()方法
　　最新的ES6中新增了Map(),Set()这两种数据结构，以及Array.from()方法，它们底层都是用红黑树实现（一种严格意义上平衡二叉树），它们的操作时间复杂度也就是红黑树的操作时间复杂度都为O(logN)。   
　　Set()和Map()的区别在于，Set()中的数据没有重复，这个特性刚好可以用于数组去重，且效率非常高，这么好的数据结构在ES6才被实现，真是可惜。   
　　下面看代码，非常简洁：
```
let arr1 = Array.from(new Set(arr1));
//NodeJS中已经全面支持ES6，这种方法爽的不要不要的。
```
#### 总结
```
function newUnique(arr1) {
    return Array.from ? Array.from(new Set(arr1)) : unique(arr1);
}
//unique()为上面的Hash方法函数。
```