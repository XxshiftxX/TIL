# 요청과 응답

```javascript
const http = require('http');

http.createServer((req, res) => {
    // Something to do
});
```

`createServer` 함수를 통해 서버를 생성할 수 있음. 해당 함수는 서버 객체를 반환.

```javascript
http.createServer((req, res) => {
    // Something to do
})
.listen(8080, () => {
    // Something to do when server start
});
```

`on` 함수를 통해 `listening` 이벤트 리스너를 붙이는 것으로 `listen` 함수를 대체할 수 있음. 같은 원리로 `error` 이벤트 등도 붙일 수 있음.

```javascript
const server = http.createServer((req, res) => {
    // Something to do
});

server.on('listening', () => {
    // Something to do when server start
});
server.on('error', () => {
    // Something to do when error occurs
});
```

`createServer` 함수에 매개변수로 준 콜백 함수는 서버에 요청이 들어올 때 실행됨. `res` 매개변수에 있는 `write` 함수와 `end` 함수로 요청에 응답하여 정보를 보내는 것 같음.

이에 대해 몇가지 실험을 해 보았고 모르는 내용에 대해 공부가 필요할 듯.
- `write` 함수는 몇번이든 사용 가능하고, 사용할 때마다 이전에 write한 결과에 append된다. 아래의 코드로 서버를 작성 시 다음과 같은 응답을 얻을 수 있다.
```javascirpt
res.write("<h1>Hello World!</h1>);
res.write("<p>This is my server</p>);
```
```html
<h1>Hello World!</h1>
<p>This is my server</p>
```
- `end` 함수는 응답을 종료할 때 사용한다. `end` 함수가 실행되지 않으면 페이지는 계속 응답이 종료될 때까지 기다리기 때문에 로딩을 지속한다. 또한 `setTimeout` 등의 함수를 이용하여 `end` 함수를 일정 시간 뒤에 사용하면 내용이 중간에 추가되고 로딩이 종료된다.
- `end` 함수 이후에 다시한번 `end` 함수를 사용하면 아무런 반응이 없다.
- `end` 함수 이후에 다시한번 `write` 함수를 사용하면 다음과 같은 에러가 발생한다.
```
Error [ERR_STREAM_WRITE_AFTER_END]: write after end
    at write_ (_http_outgoing.js:572:17)
    at ServerResponse.write (_http_outgoing.js:567:10)
    at Server.http.createServer (D:\Programing\Nodejs\NodeBook\3-2\helloWorld.js:7:9)
    at Server.emit (events.js:182:13)
    at parserOnIncoming (_http_server.js:652:12)
    at HTTPParser.parserOnHeadersComplete (_http_common.js:109:17)
```