learn-express-mongoose-ejs
==========================

## 快速开始 ##

### 安装 Express ###

express 是 Node.js 上最流行的 Web 开发框架，正如他的名字一样，使用它我们可以快速的开发一个 Web 应用。我们用 express 来搭建我们的博客，打开命令行，输入：

    $ npm install -g express@3

我们需要用全局模式安装 express，因为只有这样我们才能在命令行中使用它。

注意： 虽然目前 Express 4.x 已经发布，但这里我们选择使用 Express 3.x 版本，熟悉了 Express 3.x 后，我们只需看一下 [Migrating from 3.x to 4.x](https://github.com/visionmedia/express/wiki/Migrating-from-3.x-to-4.x) 的文档即可过渡到 Express 4.x。


### 新建一个工程 ###

windows 下打开 cmd 切换到 D 盘，输入：

    express -e learn-express-mongoose-ejs

> **注意：**express 3.x 中使用 ejs 不再是 -t ejs 而是 -e，可以输入 express -h 查看。

然后输入：

    $ cd learn-express-mongoose-ejs 
	$ npm install

安装所需模块。


安装完成后输入：

    $ node app

此时命令行中会显示 **Express server listening on port 3000**，在浏览器里访问 `localhost:3000`。


至此，我们用 express 初始化了一个工程项目，并指定使用 ejs 模板引擎。

## 工程结构 ##

- **app.js**：启动文件，或者说入口文件
- **package.json**：存储着工程的信息及模块依赖，当在 dependencies 中添加依赖的模块时，运行 npm install，npm 会检查当前目录下的 package.json，并自动安装所有指定的模块
- **node_modules**：存放 package.json 中安装的模块，当你在 package.json 添加依赖的模块并安装后，存放在这个文件夹下
- **public**：存放 image、css、js 等文件
- **routes**：存放路由文件
- **views**：存放视图文件或者说模版文件


打开 app.js，让我们看看里面究竟有什么东西：



	/**
	 * Module dependencies.
	 */
	
	var express = require('express');
	var routes = require('./routes');
	var user = require('./routes/user');
	var http = require('http');
	var path = require('path');
	
	var app = express();
	
	// all environments
	app.set('port', process.env.PORT || 3000);
	app.set('views', path.join(__dirname, 'views'));
	app.set('view engine', 'ejs');
	app.use(express.favicon());
	app.use(express.logger('dev'));
	app.use(express.json());
	app.use(express.urlencoded());
	app.use(express.methodOverride());
	app.use(app.router);
	app.use(express.static(path.join(__dirname, 'public')));
	
	// development only
	if ('development' == app.get('env')) {
	  app.use(express.errorHandler());
	}
	
	app.get('/', routes.index);
	app.get('/users', user.list);
	
	http.createServer(app).listen(app.get('port'), function(){
	  console.log('Express server listening on port ' + app.get('port'));
	});


这里我们通过 `require()` 加载了 express、http、path 模块，以及 routes 文件夹下的 index.js 和 user.js 文件。更多关于模块及模块加载顺序的信息请查阅官方文档的 [Modules](http://nodejs.org/api/all.html#all_modules) 章节。


因为 express 框架是严重依赖 connect 框架（一个 Node.js 的中间件框架）创建而成的，所以我们可查阅：

- connect 文档： [http://www.senchalabs.org/connect/](http://www.senchalabs.org/connect/)
- express 文档： [http://expressjs.com/api.html](http://expressjs.com/api.html)


了解更多内容。


- **app.set('port', process.env.PORT || 3000)**：设置端口为 process.env.PORT 或 3000。
- **app.set('views', __dirname + '/views')** ：设置 views 文件夹为存放视图文件的目录，即存放模板文件的地方，__dirname 为全局变量，存储当前正在执行的脚本所在的目录。
- **app.set('view engine', 'ejs')**：设置视图模版引擎为 ejs。
- **app.use(express.favicon())**：connect 内建的中间件，使用默认的 favicon 图标，如果想使用自己的图标，需改为 app.use(express.favicon(__dirname + '/public/images/favicon.ico')); 这里我们把自定义的 favicon.ico 放到了 /public/images 文件夹下。
- **app.use(express.logger('dev'))**：connect 内建的中间件，在开发环境下使用，在终端显示简单的日志，比如在启动 app.js 后访问 localhost:3000，终端会输出：

    Express server listening on port 3000
    GET / 200 21ms - 206b
    GET /stylesheets/style.css 304 4ms
假如你去掉这一行代码，不管你怎么刷新网页，终端都只有一行 **Express server listening on port 3000**。

- **app.use(express.bodyParser())**：connect 内建的中间件，用来解析请求体，支持 application/json， application/x-www-form-urlencoded, 和 multipart/form-data。

> **注意**：最新的 Express 生成的工程也许删除了 `app.use(express.bodyParser())` ，使用了：
    
    app.use(express.json());
    app.use(express.urlencoded());

其实，`app.use(express.bodyParser())` 相当于：

	app.use(express.json());
	app.use(express.urlencoded());
	app.use(express.multipart());


- **app.use(express.methodOverride())**：connect 内建的中间件，可以协助处理 POST 请求，伪装 PUT、DELETE 和其他 HTTP 方法。
- **app.use(app.router)**：调用路由解析的规则。
- **app.use(express.static(path.join(__dirname, 'public')))**：connect 内建的中间件，设置根目录下的 public 文件夹为存放 image、css、js 等静态文件的目录。

	if ('development' == app.get('env')) {
	  app.use(express.errorHandler());
	}

开发环境下的错误处理，输出错误信息。


- **app.get('/', routes.index)**：路由控制器，如果用户访问 `/` （主页），则由 routes.index 来处理，routes/index.js 内容如下：

    
    	exports.index = function(req, res){
    	  res.render('index', { title: 'Express' });
    	};

通过 `exports.index` 导出 index 函数接口，`app.get('/', routes.index)` 相当于：

	app.get('/', function(req, res){
	  res.render('index', { title: 'Express' });
	});


- **res.render('index', { title: 'Express' })**：使用 ejs 模板引擎解析 views/index.ejs（因为我们之前通过 `app.set('views', __dirname + '/views')` 设置了模版文件默认存储在 views 文件夹下），并传入一个对象，这个对象只有一个 title 属性，它的值为字符串 Express ，即用字符串 Express 替换 views/index.ejs 中所有 title 变量，这就是我们所说的渲染视图，或者说渲染模版。后面我们将会了解更多关于模板引擎的内容。


		http.createServer(app).listen(app.get('port'), function(){
		  console.log('Express server listening on port ' + app.get('port'));
		});


这段代码的意思是创建 http 服务器并监听 3000 端口，成功后在命令行中显示 **Express server listening on port 3000**，然后我们就可以在浏览器访问 `localhost:3000` 了。

这一小节我们学习了如何创建一个工程并启动它，了解了工程的大体结构。



