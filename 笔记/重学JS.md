# 重学JS #
## 1、'use strict';  ##
在strict模式下运行的JavaScript代码，强制通过var申明变量，未使用var申明变量就使用的，将导致运行错误。
## 2、字符串 ##
- toUpperCase()把一个字符串全部变为大写；  
- toLowerCase()把字符串全部变为小写；  
- indexOf()会搜索指定字符串第一次出现的位置，并返回下标，没找到返回-1；  
- substring(0,5);回指定索引区间的子串；
## 3、数组 ##
1. arr.length;  
取得数组的长度，请注意，直接给Array的length赋一个新的值会导致Array大小的变化：  
2. Array索引  
可以通过索引把对应的元素修改为新的值，请注意，如果通过索引赋值时，索引超过了范围，同样会引起Array大小的变化。  
3. indexOf()  
用法类似字符串。  
4. slice()  
就是对应String的substring()版本，它截取Array的部分元素，然后返回一个新的Array，只传递一个参数，会截取该参数到数组末尾的所有元素，不传递参数截取全部元素==复制数组。
5. push()和pop()  
push()是向array末尾添加若干元素，pop()是把array最后一个元素删除掉。
6. unshift和shift  
和push()和pop()相反，unshift向array头部添加若干元素，shift()是把array第一个元素删除掉。
7. sort  
对array进行排序它会直接修改当前Array的元素位置
8. reverse  
反转数组
9. splice  
方法是修改Array的“万能方法”，它可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素。 
```
arr.splice(2, 3, 'Google', 'Facebook');
// 从索引2开始删除3个元素,然后再添加两个元素:
// 从索引2开始删除3个元素,然后再添加两个元素:
```
10. concat  
拼接数组，并返回一个新的数组，不修改原数组。
11. join  
join()方法是一个非常实用的方法，它把当前Array的每个元素都用指定的字符串连接起来，然后返回连接后的字符串：
```
var arr = ['A', 'B', 'C', 1, 2, 3];
arr.join('-'); // 'A-B-C-1-2-3'
```  
练习：在新生欢迎会上，你已经拿到了新同学的名单，请排序后显示：欢迎XXX，XXX，XXX和XXX同学！：
```
'use strict';
var arr = ['小明', '小红', '大军', '阿黄'];
alert('欢迎'+arr.sort().slice(0, arr.length-1) +'和'+arr.pop()+'同学');
```
## 4、对象 ##
对象是一种无序的集合数据类型，它由若干键值对组成。

1. 由于JavaScript的对象是动态类型，你可以自由地给一个对象添加或删除属性：
```
var xiaoming = {
    name: '小明'
};
xiaoming.age; // undefined
xiaoming.age = 18; // 新增一个age属性
xiaoming.age; // 18
delete xiaoming.age; // 删除age属性
xiaoming.age; // undefined
delete xiaoming['name']; // 删除name属性
xiaoming.name; // undefined
delete xiaoming.school; // 删除一个不存在的school属性也不会报错
```
2. in操作符  
检查object是否拥有某一属性（key）
## 5、判断 ##
avaScript使用if () { ... } else { ... }来进行条件判断。  
如果if的条件判断语句结果不是true或false怎么办？例如：  
```
var s = '123';
if (s.length) { // 条件计算结果为3
    //
}
```  
JavaScript把null、undefined、0、NaN和空字符串''视为false，其他值一概视为true，因此上述代码条件判断的结果是true。
## 6、Map和Set ##