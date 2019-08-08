---
title: node学习
date: 2019-07-26 14:40:55
tags:
---


# 静态文件服务器

```js
const http = require("http")
const fs = require("fs")
const path = require("path")
//读取文件的函数

http.createServer((req, response) => {
    var filePath = path.resolve(__dirname + "/index.html");
    fs.stat(filePath, (err, stats) => {
        function readFile(filePath, contentType = "text/html") {
            response.writeHead(200, { "content-type": contentType });
            //建立流对象，读文件
            var stream = fs.createReadStream(filePath);
            //错误处理
            stream.on('error', function () {
                response.writeHead(500, { "content-type": contentType });
                response.end("<h1>500 Server Error</h1>");
            });
            //读取文件 管道概念 stream流向响应
            stream.pipe(response);
            stream.on("end", () => {
                // response.end()
            })
        }

        readFile(filePath)


    })

}).listen(8080)
```
