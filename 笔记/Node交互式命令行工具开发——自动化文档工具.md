# Node交互式命令行工具开发——自动化文档工具
　　`nodejs`开发命令行工具，流程相对简单，但一套完整的命令行程序开发流程下来，还是需要下点功夫，网上资料大多零散，这篇教程意在整合一下完整的开发流程。
　　[npm](https://www.npmjs.com/)上命令行开发相关包很多，例如`minimist`、`optimist`、`nopt`、`commander.js`、`yargs`等等，使用方法和效果类似。其中用得比较多的是TJ大神的[commander](https://www.npmjs.com/package/commander)和[yargs](https://www.npmjs.com/package/yargs)，本文以`commander`为基础讲述，可以参考这篇[教程](http://yijiebuyi.com/blog/2cd3833e8551a302b4ec645031bfd3d0.html)，yargs教程可以参考[阮大神的](http://www.ruanyifeng.com/blog/2015/05/command-line-with-node.html)或者[这一篇](https://ideras.me/a-complete-guide-to-yargs/index.html)。
　　另外，一个完整的命令行工具开发，还需要了解`process`、`shelljs`、`path`、`linebyline`等模块，这些都是`node`基础模块或一些简单模块，非常简单，就不多说了，另外如果你不想用回调函数处理异步还需要了解一下`Promise`、`Generator`函数。这是教程：`i5ting`大神的[《深入浅出js（Node.js）异步流程控制》](https://github.com/i5ting/asynchronous-flow-control?utm_source=tuicool&utm_medium=referral)和阮大神的[异步编程教程]( http://es6.ruanyifeng.com/#docs/async)以及[promise小人书](http://liubin.github.io/promises-book/#introduction)，另外想尝试ES7 stage3阶段的`async/await`异步解决方案，可参考这篇[教程](http://think2011.net/2015/11/09/ES7-Async-Await/)，`async/await`解决方案需要`babel`转码，这是[教程](http://www.ruanyifeng.com/blog/2016/01/babel.html)。本人喜欢`async/await`(哪个`node`开发者不喜欢呢？)但不喜欢倒腾，况且`async/await`本身就是`Promise`的语法糖，所以没选择使用，据江湖消息，`nodejs`将在今年晚些时候（10月份？）支持`async/await`，很是期待。
　　以下是文章末尾实例用到的一些依赖。

```json
"dependencies": {
    "bluebird": "^3.4.1",
    "co": "^4.6.0",
    "colors": "^1.1.2",
    "commander": "^2.9.0",
    "dox": "^0.9.0",
    "handlebars": "^4.0.5",
    "linebyline": "^1.3.0",
    "mkdirp": "^0.5.1"
  }
```
　　其中`bluebird`用于`Promise`化，TJ大神的`co`用于执行`Generator`函数，`handlebars`是一种模板，`linebyline`用于分行读取文件，`colors`用于美化输出，`mkdirp`用于创建目录，另外教程中的示例是一款工具，可以自动化生成数据库和`API`接口的`markdown`文档，并通过修改`git hooks`，使项目的每次`commit`都会自动更新文档，借助了TJ大神的`dox`模块。
　　<span style="color:rgb(0, 136, 204)">所有推荐教程/教材，仅供参考，自行甄选阅读。</span>
### 安装Node
　　各操作系统下安装见[Nodejs官网](https://nodejs.org/en/)，安装完成之后用`node -v`或者`which node`等命令测试安装是否成功。`which`在命令行开发中是一个非常有用的命令，使用`which`命令确保你的系统中不存在名字相同的命令行工具，例如`which commandName`，例如`which testdev`命令返回空白那么说明`testdev`命令名称还没有被使用。
### 初始化
1. 新建一个`.js`文件，即是你的命令要执行的主程序入口文件，例如`testdev.js`。在文件第一行加入`#!/usr/bin/env node`指明系统在运行这个文件的时候使用`node`作为解释器，等价于`node testdev.js`命令。
2. 初始化`package.json`文件，使用`npm init`命令根据提示信息创建，也可以是使用`npm init -y`使用默认设置创建。创建完成之后需要修改`package.json`文件内容加入`"bin": {"testdev": "./testdev.js"}`这条信息用于告诉`npm`你的命令(`testdev`)要执行的脚本文件的路径和名字，这里我们指定`testdev`命令的执行文件为当前目录下的`testdev.js`文件。
3. 为了方便测试在`testdev.js`文件中加入代码`console.log('hello world');`，这里只是用于测试环境是否搭建成功，更加复杂的程序逻辑和过程需要按照实际情况进行编写

### 测试
　　使用`npm link`命令，可以在本地安装刚刚创建的包，然后就可以用`testdev`来运行命令了，如果正常的话在控制台会打印出`hello world`
### commander
　　TJ的[commander](https://github.com/tj/commander.js)非常简洁，`README.md`已经把使用方法写的非常清晰。下面是例子中的代码：

```js
const program = require('commander'),
  co = require('co');

const appInfo = require('./../package.json'),
  asyncFunc = require('./../common/asyncfunc.js');

program.allowUnknownOption();
program.version(appInfo.version);

program
  .command('init')
  .description('初始化当前目录doc.json文件')
  .action(() => co(asyncFunc.initAction));

program
  .command('show')
  .description('显示配置文件状态')
  .action(() => co(asyncFunc.showAction));

program
  .command('run')
  .description('启动程序')
  .action(() => co(asyncFunc.runAction));

program
  .command('modifyhook')
  .description('修改项目下的hook文件')
  .action(() => co(asyncFunc.modifyhookAction));

program
  .command('*')
  .action((env) => {
    console.error('不存在命令 "%s"', env);
  });

program.on('--help', () => {
  console.log('  Examples:');
  console.log('');
  console.log('    $ createDOC --help');
  console.log('    $ createDOC -h');
  console.log('    $ createDOC show');
  console.log('');
});

program.parse(process.argv);
```
　　定义了四个命令和个性化帮助说明。
### 交互式命令行process
　　`commander`只是实现了命令行参数与回复一对一的固定功能，也就是一个命令必然对应一个回复，那如何实现人机交互式的命令行呢，类似`npm init`或者`eslint --init`这样的与用户交互，交互之后根据用户的不同需求反馈不同的结果呢。这里就需要`node`内置的`process`模块。
　　这是我实现的一个`init`命令功能代码：

```js
exports.initAction = function* () {
  try {
    var docPath = yield exists(process.cwd() + '/doc.json');
    if (docPath) {
      func.initRepl(config.coverInit, arr => {
        co(newDoc(arr));
      })
    } else {
      func.initRepl(config.newInit, arr => {
        co(newDoc(arr));
      })
    }
  } catch (err) {
    console.warn(err);
  }
```
　　首先检查`doc.json`文件是否存在，如果存在执行覆盖交互，如果不存在执行生成交互，`try...catch`捕获错误。
　　交互内容配置如下：

```js
    newInit:
    [
        {
            title:'initConfirm',
            description:'初始化createDOC,生成doc.json.确认？(y/n)  ',
            defaults: 'y'
        },
        {
            title:'defaultConfirm',
            description:'是否使用默认配置.(y/n)  ',
            defaults: 'y'
        },
        {
            title:'showConfig',
            description:'是否显示doc.json当前配置？(y/n)  ',
            defaults: 'y'
        }
    ],
    coverInit:[
        {
            title:'modifyConfirm',
            description:'doc.json已存在，初始化将覆盖文件.确认？(y/n)  ',
            defaults: 'y'
        },
        {
            title:'defaultConfirm',
            description:'是否使用默认配置.(y/n)  ',
            defaults: 'y'
        },
        {
            title:'showConfig',
            description:'是否显示doc.json当前配置？(y/n)  ',
            defaults: 'y'
        }
    ],
```
　　人机交互部分代码也就是`initRepl`函数内容如下：

```js
//初始化命令，人机交互控制
exports.initRepl = function (init, func) {
  var i = 1;
  var inputArr = [];
  var len = init.length;
  process.stdout.write(init[0].description);
  process.stdin.resume();
  process.stdin.setEncoding('utf-8');
  process.stdin.on('data', (chunk) => {
    chunk = chunk.replace(/[\s\n]/, '');
    if (chunk !== 'y' && chunk !== 'Y' && chunk !== 'n' && chunk !== 'N') {
      console.log(config.colors.red('您输入的命令是： ' + chunk));
      console.warn(config.colors.red('请输入正确指令：y/n'));
      process.exit();
    }
    if (
      (init[i - 1].title === 'modifyConfirm' || init[i - 1].title === 'initConfirm') &&
      (chunk === 'n' || chunk === 'N')
    ) {
      process.exit();
    }
    var inputJson = {
      title: init[i - 1].title,
      value: chunk,
    };
    inputArr.push(inputJson);
    if ((len--) > 1) {
      process.stdout.write(init[i++].description)
    } else {
      process.stdin.pause();
      func(inputArr);
    }
  });
}
```
　　人机交互才用向用户提问根据用户不同输入产生不同结果的形式进行，顺序读取提问列表并记录用户输入结果，如果用户输入`n/N`则终止交互，用户输入非法字符（除`y/Y/n/N`以外）提示输入命令错误。
### 文档自动化
　　文档自动化，其中数据库文档自动化，才用依赖`sequelize`的方法手写（根据需求不同自行编写逻辑），`API`文档才用TJ的[dox](https://github.com/tj/dox)也很简单。由于此处代码与命令行功能相关度不大，请读者自行去示例地址查看代码。
### 示例地址
[github地址](https://github.com/threerocks/buildDOC)
[npm地址]( https://www.npmjs.com/package/createDOC)
### 工具说明
## 简介

工具可以自动化生成数据库和API接口的markdown文档，并通过修改git hooks，使项目的每次commit都会自动更新文档。

## 安装

 `npm i createDOC -g`

## 配置
 - 在项目根目录使用`createDOC init`命令初始化，该命令会在当前目录创建`doc.json`文件。
 - 生成`doc.json`文件后，需要详细配置数据库schemas存储路径(目前只支持关系型数据库)，以及路由控制文件，以及子路由目录。
 - API注释规则，遵循TJ大神dox规范，简化版规则如下

 ```js
 /**
 * API description
 *
 * @param {type} name/name=default_value description 
 * @return {String} description
 * @example
 *     any example
 *     
 * @other description
 */
 ```

*ps: 此工具为内部使用工具，如个人使用可下载源码，做简单修改即可*
## 使用

```sh
  Usage: createDOC [options] [command]

  Commands:

    init         初始化当前目录doc.json文件
    show         显示配置文件状态
    run          启动程序
    modifyhook   修改项目下的hook文件
    *

  Options:

    -h, --help     output usage information
    -V, --version  output the version number

  Examples:

    $ createDOC --help
    $ createDOC -h
    $ createDOC show
```

## 示例说明
doc.json示例

```json
{
    "db": {
        "schemas": "/Users/mac/Desktop/testssss/schemas",
        "markdown": {
            "path": "/Users/mac/Desktop/testssss/doc1/",
            "file": "db.md"
        }
    },
    "api": {
        "controller": "/Users/mac/Desktop/testssss",
        "routes": "/Users/mac/Desktop/testssss",
        "markdown": {
            "path": "/Users/mac/Desktop/testssss/doc1",
            "file": "api.md"
        }
    }
}
```
schema.js示例

```js
module.exports = function(sequelize, DataTypes) {
   	return sequelize.define('test_zk_absence', {
   		//这是id
   		id: {
   			type: DataTypes.INTEGER,
   			allowNull: false,
   			primaryKey: true,
   			autoIncrement: true
   		},
   		//这是end_data
   		end_date: {
   			type: DataTypes.DATE,
   			allowNull: true
   		},
   		/*
   		{
   			a:1,
   			b:2,
   			c:{a:2},
   			d:'2222'
   		}
   		*/
   		type_id: {
   			type: DataTypes.INTEGER,
   			allowNull: true
   		},
   		updated_at: {
   			type: DataTypes.DATE,
   			allowNull: false
   		}
   	}, {
   		tableName: 'test_zk_absence'
   	});
   };
```
api注释示例

```js
/**
   * 获取多个课程
   * @param {Number} examinationId 考试类型
   * @param {Number} subjectId 科目类型
   * @param {Number} statusId=3 状态类型
   * @param {String} startDate 更新开始时间
   * @param {String} endDate 更新结束时间
   * @param {String} keyword 关键词
   * @param {Number} page 页码
   * @return {Array} ok
   * @example [1,2,3,3,4]
   */
getCourses(req, params) {
		... ...
}
```
## TODO
1. 代码逻辑优化，适应力更强。
2. 代码速度、质量优化。
3. 加入单元测试
  



