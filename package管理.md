## Package管理
后台npm
（不多说）
前台bower
简介：
Bower的功能类似于Nodejs中的npm或者Python中的pip，用于web包管理，如果越来越多得开源项目都托管在github上，bower只需要将github上项目加上一个配置文件既可以使用bower方式使用安装包。作为包管理，bower能提供添加新web包，更新web包，删除web包，发布web包功能，管理包依赖。web包通常认为由html+css+javascript构成。使用：
根据上面的简介描述，bower依赖于nodejs，下载安装依赖库实际上是使用git进行下载，因此使用bower前确保安装了Node和Git环境，并且建议使用git bash命令行来执行bower install命令。
（常用npm、node、大神略过此段）另外可能我们需要在任何目录使用bower命令的话，需要配置npm环境变量，这一步很重要，不然会提示“bower”不是内部或外部命令，配置npm环境变量就很简单了，在path目录下添加npm全局的包的安装路径但不包含node_modules目录，另外新建NODE_PATH，值为全局的包的安装路径包含node_modules目录，例如：path：D:\node，NODE_PATH：D:\node\node_modules，这样我们npm install XXX –g的安装包才能在全局使用，不会出现提示“XXX不是内部或外部命令”。
好了，废话不多说，bower使用就很简单了：
安装bower
npm install bower -g
在项目目录中运行
bower install jquery
运行成功之后项目中会多出components文件夹，文件夹中jquery文件夹，jquery文件夹里面就有最新的jquery文件。
这还不能说明他NB的地方，试想下面的场景，jQuery升级了，是不是再down一次jQuery呢？bower可以这样做：
bower update jquery
就可以自动升级到最新版的jquery了。
再假设我们需要使用bootstrap，bootstrap可不是一个文件，有css,js还有图片。js还依赖于jQuery，如果使用bower：
bower install bootstrap
bower会自动从github上down最新的代码，而且，会自动将依赖包jquery也down一次。
如果你发布程序的时候不想把一些依赖的库发布上去（主要原因是太大了 – – ），可以在项目根目录下生成一个 bower.json 文件用来管理依赖。
在项目目录下执行
bower init
按照提示操作就好，这样子会生成一个bower文件
安装 jquery
bower install jquery --save
这样子 bower.json 文件就会写入一个 Jquery的依赖项
别人只要在项目目录下输入
bower install
就会自动安装了
