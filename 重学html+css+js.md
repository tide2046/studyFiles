## Html
1、<q></q>短文本引用，自动加双引号，语义是引用。
2、<blockquote></blockquote>长文本引用，自动两端缩进。
3、<br><br />为换行，&nbsp;为空格，<hr />为水平横线。
4、<address></address>地址。
5、<code></code>当代码为一行代码时，你就可以使用<code>标签了。
6、<pre></pre>语言代码段，多行。
7、<table summary="表格简介文本">，<caption>标题文本</caption>
8、<a href="目标网址" target="_blank">click here!</a>新建浏览器窗口中打开链接。
9、<a href=”mailto: yy@qq.com?cc=xx@qq.com&bcc=zz@qq.com&subject=主题&body=内容”>发送</a>
（第一个参数用？隔开后边参数用&隔开，cc：抄送。Bcc：密抄送。Subject：标题。Body：内容）
10、<img src=” ” alt=” ” title=” ”> src为图片地址；alt为下载不成功时描述性文本；title为图像可见时图像的描述。
11、<form   method="传送方式"   action="服务器文件">
12、<textarea  rows="行数" cols="列数">文本</textarea>文本域。
13、<input type="radio/checkbox" value="值" name="名称"   checked="checked"/>单选框、复选框。同一组的单选按钮，name 取值一定要一致，这样同一组的单选按钮才可以起到单选的作用。
14、<select><option value=”向服务器提交的值” selected=”selected”>显示的值</option></select>下拉框。multiple="multiple"使用下拉列表框进行多选。
15、<input type="submit" value="确定"  />按钮，<input type="reset" value="重置"  />重置按钮。
16、<label for="控件id名称">当用户单击选中该label标签时，浏览器就会自动将焦点转到和标签相关的表单控件上。
注意：标签的 for 属性中的值应当与相关控件的 id 属性值一定要相同。

## CSS
1、>作用于元素的第一代后代，空格作用于元素的所有后代。>适合包含样式时仅设置第一代后代样式。
2、伪类选择符a:hover{color:red;}鼠标滑过的状态。
3、有些特殊的情况需要为某些样式设置具有最高权值，怎么办？这时候我们可以使用!important来解决。
p{color:red!important;}
p{color:green;}
4、文字排版
字体p{font-family:"Microsoft Yahei";}现在一般网页喜欢设置“微软雅黑”
文字粗体p{font-weight:bold;}
文字斜体p{font-style:italic;}
文字下划线p{text-decoration:underline;}
文字删除线p{text-decoration:line-through;}
文字缩进p{text-indent:2em;} 2em的意思就是文字的2倍大小。
文字行间距p{line-height:1.5em;}
中文间距h1{letter-spacing:50px;} 字母间距h1{word-spacing:50px;}
5、段落排版
对齐h1{text-align:center;} {text-align:left;} {text-align:right;}
6、元素分类
常用的块状元素有：
<div>、<p>、<h1>...<h6>、<ol>、<ul>、<dl>、<table>、<address>、<blockquote> 、<form>
常用的内联元素有：
<a>、<span>、<br>、<i>、<em>、<strong>、<label>、<q>、<var>、<cite>、<code>
常用的内联块状元素有：
<img>、<input>
7、元素分类--块级元素
每个块级元素都从新的一行开始，并且其后的元素也另起一行。
元素的高度、宽度、行高以及顶和底边距都可设置。
元素宽度在不设置的情况下，是它本身父容器的100%（和父元素的宽度一致），除非设定一个宽度。
将内联元素a转换为块状元素a{display:block;}
8、元素分类--内联元素
和其他元素都在一行上；
元素的高度、宽度及顶部和底部边距不可设置；
元素的宽度就是它包含的文字或图片的宽度，不可改变。
将块状元素设置为内联元素
div{display:inline;}
9、元素分类--内联块状元素
就是同时具备内联元素、块状元素的特点，代码display:inline-block就是将元素设置为内联块状元素。
10、盒子模型—块级标签
盒子和内容间隙叫padding,内边距,有四个方向top,right,bottom,left
盒子和盒子之间得间隙叫margin,外边距,有四个方向top,right,bottom,left
边框border有四个方向top,right,bottom,left
border-style（边框样式）常见样式有：dashed（虚线）| dotted（点线）| solid（实线）。
border-color（边框颜色）中的颜色可设置为十六进制颜色，如:border-color:#888;//前面的井号不要忘掉。
border-width（边框宽度）中的宽度也可以设置为：thin | medium | thick（但不是很常用），最常还是用象素（px）。
简写div{border:2px  solid  red;}
11、CSS布局模型
布局模型与盒模型一样都是 CSS 最基本、 最核心的概念。
在网页中，元素有三种布局模型：
1)流动模型（Flow）
2)浮动模型 (Float)
3)层模型（Layer）
CSS定义了一组定位（positioning）属性来支持层布局模型。
层模型有三种形式：
1)绝对定位(position: absolute)
2)相对定位(position: relative)
3)固定定位(position: fixed)
绝对定位，相对于父对象真实移动，如果没有父对象，父对象是body
.a{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%)}
相对定位，相对于该元素原始位置便宜，且偏移前的位置还保留不动，覆盖不了前面的div没有偏移前的位置。
固定定位，fixed：表示固定定位，与absolute定位类型类似，但它的相对移动的坐标是视图（屏幕内的网页窗口）本身。不会受文档流动影响。比如固定当前窗口的小广告

12、盒模型简写
margin:10px 15px 12px 14px;/*上设置为10px、右设置为15px、下设置为12px、左设置为14px*/
1、如果top、right、bottom、left的值相同，如下面代码：
margin:10px 10px 10px 10px;
可缩写为：
margin:10px;
2、如果top和bottom值相同、left和 right的值相同，如下面代码：
margin:10px 20px 10px 20px;
可缩写为：
margin:10px 20px;
3、如果left和right的值相同，如下面代码：
margin:10px 20px 30px 20px;
可缩写为：
margin:10px 20px 30px;
注意：padding、border的缩写方法和margin是一致的。
13、字体缩写
body{
    font-style:italic;
    font-variant:small-caps; 
    font-weight:bold; 
    font-size:12px; 
    line-height:1.5em; 
    font-family:"宋体",sans-serif;
}
这么多行的代码其实可以缩写为一句：
body{
    font:italic  small-caps  bold  12px/1.5em  "宋体",sans-serif;
}
1、使用这一简写方式你至少要指定 font-size 和 font-family 属性，其他的属性(如 font-weight、font-style、font-varient、line-height)如未指定将自动使用默认值。
2、在缩写时 font-size 与 line-height 中间要加入“/”斜扛。
一般情况下因为对于中文网站，英文还是比较少的，所以下面缩写代码比较常用：
body{
    font:12px/1.5em  "宋体",sans-serif;
}
只是有字号、行间距、中文字体、英文字体设置。
14、长度值
px（像素）、em、% 百分比，要注意其实这三种单位都是相对单位。
像素指的是显示器上的小点
em就是本元素给定字体的 font-size 值，如果元素的 font-size 为 14px ，那么 1em = 14px；如果 font-size 为 18px，那么 1em = 18px。
p{font-size:12px;line-height:130%}设置行高（行间距）为字体的130%（12 * 1.3 = 15.6px）。
15、居中
水平居中，内联元素水平居中text-align：center。
当被设置元素为块状元素时用 text-align：center 就不起作用了。这时也分两种情况：定宽块状元素和不定宽块状元素。
满足定宽和块状两个条件的元素是可以通过设置“左右margin”值为“auto”来实现居中的。
不定宽度的块状元素有三种方法居中（这三种方法目前使用的都比多）：
加入 table 标签
设置 display;inline 方法
设置 position:relative 和 left:50%;
垂直居中-父元素高度确定的单行文本
设置父元素的 height 和 line-height 高度一致来实现
垂直居中-父元素高度确定的多行文本
1、	vertical-align是垂直居中属性，但只有父元素为tr和td的时候才有用。因此可以用table包含多行文本，实现垂直居中。注意：td 标签默认情况下就默认设置了 vertical-align 为 middle。
2、	通过设施display:table-cell; 激活 vertical-align 属性，但注意 IE6、7 并不支持这个样式。
display:table-cell;/*IE8以上及Chrome、Firefox*/
vertical-align:middle;/*IE8以上及Chrome、Firefox*/
