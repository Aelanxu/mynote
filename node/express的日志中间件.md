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
