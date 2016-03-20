## 正则表达式学习
.就是一个占位符，匹配除'\n'之外的任意字符。
*匹配前面字符0次或任意次
?匹配前一个字符0次或1次
-----------------------------------------------------------------
上面的内容全部只需要了解即可，需要掌握的只有下面这一种组合方式(.*?)
.* 贪心算法，尽可能多的匹配，像一个胖子尽可能多的吃
.*?非贪心算法，尽可能少的匹配，像一个婴儿少量多餐
例如：code = 'hhosahfoxxIxxohofhjosfhxxlovexxoruowhntlnmlxxyouxxljh'
     re.findall('xx.*xx',code)匹配第一个xx到最后一个xx，即xxIxxohofhjosfhxxlovexxoruowhntlnmlxxyouxx
     re.findall('xx.*?xx',code)匹配结果['xxIxx','xxlovexx','xxyouxx']
(.*?)括号内的数据作为结果返回
例如：d = re.findall('xx(.*?)xx',code)
     for each in d
         print each
打印结果：
I
love
you
-----------------------------------------------------------------
re.S
例如：code = 'hhosahfoxxI
     xxohofhjosfhxxlovexxoruowhntlnmlxxyouxxljh'
     d = re.findall('xx(.*?)xx',code)
        print d
打印结果['ohofhjosfh','oruowhntlnml']
解释：.号可以匹配任意字符，但除了换行符'\n'
这时候就需要re.S
例如：d = re.findall('xx(.*?)xx',code,re.S)
     print d
打印结果['I\n','love','you']
可以看出re.S的作用使.包括\n
其他：
重点掌握三个
re.I	使匹配对大小写不敏感
re.L	做本地化识别（locale-aware）匹配
re.M	多行匹配，影响 ^ 和 $
-----------------------------------------------------------------
对比findall与search的区别
s2 = 'asdfxxIxx123xxlovexxdfdasdfxxIxx123xxlovexxdfd'
f = re.search('xx(.*?)xx123xx(.*?)xx',s2)
print f.group(2)
if not f:
    print 'not'
else:
    print 'yes'
输出结果：
love
yes
注：re.serch与re.match()区别是re.match()只匹配字符串的开始，则匹配失败，函数返回None
f2 = re.findall('xx(.*?)xx123xx(.*?)xx',s2)
print f2
输出结果：
[('I', 'love'), ('I', 'love')]
注：findall返回一个list，有多次匹配在list中嵌套tuple(元组)
-----------------------------------------------------------------
sub的使用
s = '123abccssfadjfdj123'
output = re.sub('123(.*?)123','123%d123%789',s)
print output
-----------------------------------------------------------------
常用技巧
1、在确定只有一个内容时，使用search方法可以提高效率
2、不要要使用compile
str = 'asdfxxIxx123xxlovexxdfdasdfxxIxx123xxlovexxdfd'
pattern = 'xx(.*?)xx'
new_pattern = re.compile(pattern,re.S)
output = re.findall(new_pattern,str)
3、匹配数字使用\d+
a = 'asdsds123456fasd888fas'
b = re.findall('(\d+)',a)
print b
执行结果：['123456','888']
-----------------------------------------------------------------
总结
re.match 尝试从字符串的起始位置匹配一个模式。
re.search 扫描整个字符串并返回第一个成功的匹配。
re.match只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回None；而re.search匹配整个字符串，直到找到一个匹配。
re.sub用于替换字符串中的匹配项。
re.split按照能够匹配的子串将string分割后返回列表。
re.findall搜索string，以列表形式返回全部能匹配的子串。
re.finditer搜索string，返回一个顺序访问每一个匹配结果（Match对象）的迭代器。


re.I	使匹配对大小写不敏感
re.L	做本地化识别（locale-aware）匹配
re.M	多行匹配，影响 ^ 和 $
re.S	使 . 匹配包括换行在内的所有字符
re.U	根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B.
re.X	该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解。

.	匹配除 "\n" 之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，请使用象 '[.\n]' 的模式。
\d	匹配一个数字字符。等价于 [0-9]。
\D	匹配一个非数字字符。等价于 [^0-9]。
\s	匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。
\S	匹配任何非空白字符。等价于 [^ \f\n\r\t\v]。
\w	匹配包括下划线的任何单词字符。等价于'[A-Za-z0-9_]'。
\W	匹配任何非单词字符。等价于 '[^A-Za-z0-9_]'。

[Pp]ython	匹配 "Python" 或 "python"
rub[ye]	匹配 "ruby" 或 "rube"
[aeiou]	匹配中括号内的任意一个字母
[0-9]	匹配任何数字。类似于 [0123456789]
[a-z]	匹配任何小写字母
[A-Z]	匹配任何大写字母
[a-zA-Z0-9]	匹配任何字母及数字
[^aeiou]	除了aeiou字母以外的所有字符
[^0-9]	匹配除了数字外的字符

.	点	匹配单个任意字符
[...]	字符组	匹配单个字符组出现的字符
[^...]	排除型字符组	匹配单个未在字符组出现的字符
\char	转义符	如果char是元字符或没有特殊含义， 则匹配char。
?	问号	容许非必须的一次匹配
+	加号	匹配1次以上
*	星号	匹配任意次
{min,max}	区间量词	匹配min次到max次。
^$	脱字符和美元符	匹配一行文本的开头和结束。
\b	边界符	匹配单词的边界
|	|	多选结构
(...)	括号	用处有三，请看上面
\1.\2	反向引用	对括号捕捉到的分组进行引用。

括号
如果需要使用分组，或者对一个分组进行量词限定，就可以使用它了。使用括号以后，可以：
在后面进行反向引用
作为一个子表达式使用
限制多选结构
-----------------------------------------------------------------