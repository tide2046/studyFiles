### 主要是自己使用mongoose的一些有疑虑的地方简单介绍
　　初次使用mongoose的过程中有很多的疑惑，MongoDB是文档型数据库，文档保存在集合中，所有的增删改查都是在集合中操作，而在mongoose中很多操作并没有集合的概念，因此在集合的创建、查询、删除等等操作的时候，我就会想Collections在哪？相信初学者都会有这样的疑惑。直到翻看了官方文档中的一句话，“With Mongoose, everything is derived from a Schema.”注意，是everything。记住这句话，我相信不管是学习还是使用mongoose，那么思考的方向已经对了。  
　　使用mongoose连接MongoDB就不多说了，直接上代码：  
```
	var mongoose = require(‘mongoose’);
	// 引入 mongoose 模块
	mongoose.connect(‘mongodb://localhost/test);
	// 然后连接对应的数据库：mongodb://localhost/test
	// 其中，前面那个 mongodb 是 protocol scheme 的名称；localhost 是 mongod 所在的地址；
	// 端口号省略则默认连接 27017；test 是数据库的名称
	// mongodb 中不需要建立数据库，当你需要连接的数据库不存在时，会自动创建一个出来。
	var db = mongoose.connection;
	db.on(‘error’, console.error.bind(console, ‘connection error:’));
	db.once(‘open’, function (callback) {
	console.log(“MongoDB Opened!”);
	});
	module.exports = mongoose;
```
　　值得注意的是，考虑任何问题，尤其是我们码农在程序中考虑任何问题，都要考虑发生错误怎么办，使用mongoose也不例外。
```
	var mongoose = require(’…/modules/db’);
	var impressionSchema = mongoose.Schema({
	 impression:{type:String},
	 author:{type:String}
	});
	var impressionModel = mongoose.model(‘friendimpression’,impressionSchema);
	var impression = new impressionModel({impression:“fool”,author:“sweety”});
	impression.save(function (err) {
	 if (err) return console.error(err);
	 console.log(“Save Successful!”);
	});
```
　　这段代码是保存一个文档的实例，在这段代码中我的疑虑是我们把文档保存在哪个Collection里边？答案仍然是“With Mongoose, everything is derived from a Schema.”，mongoose是通过model来创建mongodb中对应的collection的，mongoose在内部创建collection时将我们传递的collection名（‘friendimpression’）小写化，同时如果小写化的名称后面没有字母——s,则会在其后面添加一s,针对我们刚建的collection,则会命名为：friendimpressions。
```
	impressionModel.find(function (err, docs) {
   		 if (err) return console.error(err);
    		console.log(docs)
		});
	};
```
　　查询的疑虑在于，我如果对存在的表进行查询怎么办？我必须先创建model？可是我不需要插入删除等操作，我只想查询一下就完事儿。看一下mongoose官方的查询方法Model.find、Model.findOne、Model.findById、Model.where、Model.$where等等，都是基于Model，Model又来自Schema，其实Schema就是你文档结构的规则，俗话说无规矩不成方圆，MongoDB也是一样，不关你是查询还是插入、删除都得有个规矩，所以即使是简单的查询也要从Schema开始，有了规矩增删改查起来也方便很多。