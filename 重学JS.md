## 重学JS
1、'use strict';
在strict模式下运行的JavaScript代码，强制通过var申明变量，未使用var申明变量就使用的，将导致运行错误。
2、字符串
toUpperCase()把一个字符串全部变为大写；
toLowerCase()把字符串全部变为小写；
indexOf()会搜索指定字符串第一次出现的位置，并返回下标，没找到返回-1；
substring(0,5);回指定索引区间的子串；
3、数组
arr.length;取得数组的长度，请注意，直接给Array的length赋一个新的值会导致Array大小的变化：
Array可以通过索引把对应的元素修改为新的值，请注意，如果通过索引赋值时，索引超过了范围，同样会引起Array大小的变化。
indexOf()用法类似字符串。
slice()就是对应String的substring()版本，它截取Array的部分元素，然后返回一个新的Array，只传递一个参数，会截取该参数到数组末尾的所有元素，不传递参数截取全部元素===复制数组。