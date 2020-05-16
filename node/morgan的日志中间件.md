## node的控制台日志
morgan是express默认的日志中间件，也可以脱离express，作为node.js的日志组件单独使用。
1. 安装
```shell
npm install express morgan --save
```
2. 使用
```javascript
var express = require('express');
var app = express();
var morgan = require('morgan');

app.use(morgan('short'));
app.use(function(req, res, next){
    res.send('ok');
});
```
3. 将日志打印到本地文件
```javascript
var express = require('express');
var app = express();
var morgan = require('morgan');
var fs = require('fs');
var path = require('path');

var accessLogStream = fs.createWriteStream(path.join(__dirname, 'access.log'), {flags: 'a'});

app.use(morgan('short', {stream: accessLogStream}));
app.use(function(req, res, next){
    res.send('ok');
});

app.listen(3000);
```
4. 日志切割
   借助file-stream-rotator插件，可以轻松完成日志分割的工作。除了file-stream-rotator相关的配置代码，其余跟之前的例子差不多
```javascript
   var FileStreamRotator = require('file-stream-rotator')
var express = require('express')
var fs = require('fs')
var morgan = require('morgan')
var path = require('path')

var app = express()
var logDirectory = path.join(__dirname, 'log')

// ensure log directory exists
fs.existsSync(logDirectory) || fs.mkdirSync(logDirectory)

// create a rotating write stream
var accessLogStream = FileStreamRotator.getStream({
  date_format: 'YYYYMMDD',
  filename: path.join(logDirectory, 'access-%DATE%.log'),
  frequency: 'daily',
  verbose: false
})

// setup the logger
app.use(morgan('combined', {stream: accessLogStream}))

app.get('/', function (req, res) {
  res.send('hello, world!')
})
```
5.  日志写入数据库
   有的时候，我们会有这样的需求，将访问日志写入数据库。这种需求常见于需要实时查询统计的日志系统。
   通过stream指定日志的输出流。
```javascript
var accessLogStream = fs.createWriteStream(path.join(__dirname, 'access.log'), {flags: 'a'});
app.use(morgan('short', {stream: accessLogStream}));
```
在morgan内部，大致实现是这样的（简化后）
```javascript
// opt为配置文件
var stream = opts.stream || process.stdout;
var logString = createLogString();  // 伪代码，根据format、token的定义，生成日志
stream.write(logString);
```
于是，可以用比较取巧的方式来实现目的：声明一个带write方法的对象，并作为stream配置传入。
```javascript
var express = require('express');
var app = express();
var morgan = require('morgan');

// 带write方法的对象
var dbStream = {
  write: function(line){
    saveToDatabase(line);  // 伪代码，保存到数据库
  }
};

// 将 dbStream 作为 stream 配置项的值
app.use(morgan('short', {stream: dbStream}));
app.use(function(req, res, next){
  res.send('ok');
});

app.listen(3000);

```