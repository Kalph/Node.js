### 참조 사이트
 
[(NodeJS) HTML 전송하기 - ZeroCho Blog](https://www.zerocho.com/category/NodeJS/post/5787408cc27c3ae07521f848)

<br>
<hr>

## test.js

```py3
const http = require('http');
const url = require('url');
const fs = require('fs');
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;
var result =''

// db
var config = {
    server: '127.0.0.1',
    port:'port',
    options: { encrypt:false, database: 'DBName' },
    authentication:{
        type:"default",
        options:{
            userName:"user_name",
            password:"1234"
        }
    }
};

var sqlConn = new Connection(config);
sqlConn.on('connect', function(err) {
    console.log("Connected");
    executeStatement();
});

function executeStatement() {
    request = new Request("select * from general where main_Name='DA-402-1'", function(err, rowCount) {
        if (err) {
            console.log(err);
        } else {
            console.log(rowCount + ' rows');
        }
    });

    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log(column.value)
            result+=column.value+" "
        });
    });
    sqlConn.execSql(request);
}

// html
http.createServer((request, response) => {
  const path = url.parse(request.url, true).pathname; // url에서 path 추출
  if (request.method === 'GET') { // GET 요청이면
    if (path === '/test') { // 주소가 /test2이면
      response.writeHead(200,{'Content-Type':'text/html'}); // header 설정
      response.write("<h3>"+result+"</h3>");
      fs.readFile(__dirname + '/test2.html', (err, data) => { // 파일 읽는 메소드
        if (err) {
          return console.error(err); // 에러 발생시 에러 기록하고 종료
        }
        response.end(data, 'utf-8'); // 브라우저로 전송
      });
    } else if (path === '/') { // 주소가 /이면
      response.writeHead(200,{'Content-Type':'text/html'});
      response.write("<h3>"+result+"</h3>");
      fs.readFile(__dirname + '/test2.html', (err, data) => {
        if (err) {
          return console.error(err);
        }
        response.end(data, 'utf-8');
      });
    } else { // 매칭되는 주소가 없으면
      response.statusCode = 404; // 404 상태 코드
      response.end('주소가 없습니다');
    }
  }
}).listen(3000);
```

<br>
<hr>

## launch.json

<br>

```py3
{
    // IntelliSense를 사용하여 가능한 특성에 대해 알아보세요.
    // 기존 특성에 대한 설명을 보려면 가리킵니다.
    // 자세한 내용을 보려면 https://go.microsoft.com/fwlink/?linkid=830387을(를) 방문하세요.
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "프로그램 시작",
            "skipFiles": [
                "<node_internals>/**"
            ],
            "program": "${workspaceFolder}\\test.js"
        }
    ]
}
```
