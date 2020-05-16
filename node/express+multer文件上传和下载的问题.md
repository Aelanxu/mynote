## node使用express+multer文件上传和下载的问题

node使用express+multer文件上传和下载的问题
1. 文件上传使用multer，首先要在项目中安装multer模块。
``` shell
npm install multer -save
```
2. 这里使用express模块,multer文件上传分为单文件和多文件，单文件使用如下；

1) 客户端代码,这里我们简单使用form表单提交，使用post方式，表单一定要设置enctype="multipart/form-data",否则会有意想不到的错误。
```html
<h1>文件上传</h1>
<form action="/" method="post" enctype="multipart/form-data">
    <input id="files" type="file" name="file" multiple/> <!-- 这里的multiple属性是允许多文件上传的，单文件上传可以不写此属性（最好都写上）-->
    <input type="submit" value="上传"/>
</form>
```
2) 客户端到这里就可以了，下面进行服务端配置，单文件要是用，**single("在这里写表单里的name值")**

```javascript
let express = require('express');
let multer = require('multer');
let fs = require("fs");
let path = require("path");
let router = express.Router();

router.post('/', multer({
      //设置文件存储路径
     dest: 'upload'   //upload文件如果不存在则会自己创建一个。
 }).single('file'), function (req, res, next) {
    if (req.file.length === 0) {  //判断一下文件是否存在，也可以在前端代码中进行判断。
        res.render("error", {message: "上传文件不能为空！"});
        return
    } else {
       let file = req.file;
       let fileInfo = {};
       console.log(file);
       fs.renameSync('./upload/' + file.filename, './upload/' + file.originalname);//这里修改文件名字，比较随意。
       // 获取文件信息
       fileInfo.mimetype = file.mimetype;
       fileInfo.originalname = file.originalname;
       fileInfo.size = file.size;
       fileInfo.path = file.path;

       // 设置响应类型及编码
       res.set({
         'content-type': 'application/json; charset=utf-8'
      });

       res.end("上传成功！");
    }
 });
```

3. 下面则是多文件的使用，记住前面说的input表单multiple属性;
1) 客户端代码和上面一样
```html
<h1>文件上传</h1>
<form action="/" method="post" enctype="multipart/form-data">
    <input id="files" type="file" name="file" multiple/> <!-- 这里的multiple属性是允许多文件上传的,一点要写-->
    <input type="submit" value="上传"/>
</form>
```
2) 服务端的代码不使用single 而是使用.array("表单name属性","这里填写最大支持的文件数目")，使用方式如下
```javascript
router.post('/', multer({
    //设置文件存储路径
    dest: 'upload'
}).array('file', 10), function (req, res, next) {  //这里10表示最大支持的文件上传数目
    let files = req.files;
    if (files.length === 0) {
        res.render("error", {message: "上传文件不能为空！"});
        return
    } else {
        let fileInfos = [];
        for (var i in files) {
            let file = files[i];
            let fileInfo = {};


            fs.renameSync('./upload/' + file.filename, './upload/' + file.originalname);//这里修改文件名。

            //获取文件基本信息
            fileInfo.mimetype = file.mimetype;
            fileInfo.originalname = file.originalname;
            fileInfo.size = file.size;
            fileInfo.path = file.path;

            fileInfos.push(fileInfo);
        }
        // 设置响应类型及编码
        res.set({
            'content-type': 'application/json; charset=utf-8'
        });
        res.end("success!");
    }
});
```

4.此时文件的简单上传实现了。当然这只是简单的进行上传，有时候还要考虑文件的大小，磁盘空间等等，当然上传文件也不只有一种，还有使用[formidable](https://www.npmjs.com/package/formidable), [multiparty](https://www.npmjs.com/package/multiparty)等，有兴趣可以查看官方文档详细介绍。上传文件当然就要有下载，有时候压缩文件可以直接通过a标签下载，但是有时如果是图片，之类的浏览器会直接打开，很不友好，所以有专门的下载。

下载主要有两种方式，也比较简单，直接演示
```javascript
//第一种方式
  let road="这里是要下载的文件路径（可以是中文，相对路径，绝对路径等等）";
  res.download(road); //直接调用download方法即可

  //第二种方式
  let road="这里是要下载的文件路径（可以是中文，相对路径，绝对路径等等）";
  let road = fs.createReadStream(path); //创建输入流入口
  res.writeHead(200, {
    'Content-Type': 'application/force-download',
    'Content-Disposition': 'attachment; filename=name'
  });
  load.pipe(res);// 通过管道方式写入
```