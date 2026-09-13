# Inicialização do servidor
### indepedente
`CommonJS`
```javascript
const { Server } = require("socket.io");

const io = new Server({ /* options */ });

io.on("connection", (socket) => {
  // ...
});

io.listen(3000);
```

### Você também pode passar a porta como primeiro argumento:

`CommonJS`
```javascript
const { Server } = require("socket.io");

const io = new Server(3000, { /* options */ });

io.on("connection", (socket) => {
  // ...
});
```
<hr>

# Isso inicia implicitamente um servidor HTTP Node.js , que pode ser acessado através de io.httpServer.
### Com um Servidor HTTP

`CommonJS`
```javascript
const { createServer } = require("http");
const { Server } = require("socket.io");

const httpServer = createServer();
const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```
<hr>

### Com um Servidor HTTPS

`CommonJS`
```javascript
const { readFileSync } = require("fs");
const { createServer } = require("https");
const { Server } = require("socket.io");

const httpsServer = createServer({
  key: readFileSync("/path/to/my/key.pem"),
  cert: readFileSync("/path/to/my/cert.pem")
});

const io = new Server(httpsServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpsServer.listen(3000);
```

Veja também: Documentação do Node.js

Com autenticação por certificado de cliente:

`Servidor`
```javascript
import { readFileSync } from "fs";
import { createServer } from "https";
import { Server } from "socket.io";

const httpsServer = createServer({
  key: readFileSync("/path/to/server-key.pem"),
  cert: readFileSync("/path/to/server-cert.pem"),
  requestCert: true,
  ca: [
    readFileSync("/path/to/client-cert.pem")
  ]
});

const io = new Server(httpsServer, { /* options */ });

io.engine.on("connection", (rawSocket) => {
  // se você precisar dos detalhes do certificado (ele não fica mais disponível após a conclusão do handshake)
  rawSocket.peerCertificate = rawSocket.request.client.getPeerCertificate();
});

io.on("connection", (socket) => {
  console.log(socket.conn.peerCertificate);
  // ...
});

httpsServer.listen(3000);
```

`Cliente`
```javascript
import { readFileSync } from "fs";
import { io } from "socket.io-client";

const socket = io("https://example.com", {
  key: readFileSync("/path/to/client-key.pem"),
  cert: readFileSync("/path/to/client-cert.pem"),
  ca: [
    readFileSync("/path/to/server-cert.pem")
  ]
});
```
<hr>

### Com um servidor HTTP/2
`CommonJS`
```javascript
const { readFileSync } = require("fs");
const { createSecureServer } = require("http2");
const { Server } = require("socket.io");

const httpServer = createSecureServer({
  allowHTTP1: true,
  key: readFileSync("/path/to/my/key.pem"),
  cert: readFileSync("/path/to/my/cert.pem")
});

const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```

Veja também: Documentação do Node.js
<hr>

`Com Express`
`CommonJS`
```javascript
const express = require("express");
const { createServer } = require("http");
const { Server } = require("socket.io");

const app = express();
const httpServer = createServer(app);
const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```
<hr>

### Com Koa
`CommonJS`
```javascript
const Koa = require("koa");
const { createServer } = require("http");
const { Server } = require("socket.io");

const app = new Koa();
const httpServer = createServer(app.callback());
const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```
<hr>

### Com o Fastify
`CommonJS`
```javascript
const Fastify = require("fastify");
const { Server } = require("socket.io");

const fastify = Fastify();
const io = new Server(fastify.server, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

fastify.addHook("preClose", (done) => {
  // encerrar todas as conexões ativas neste servidor
  io.local.disconnectSockets(true);
  done();
});

fastify.listen({ port: 3000 });
```
<hr>

### Com µWebSockets.js

```javascript
import { App } from "uWebSockets.js";
import { Server } from "socket.io";

const app = App();
const io = new Server();

io.attachApp(app);

io.on("connection", (socket) => {
  // ...
});

app.listen(3000, (token) => {
  if (!token) {
    console.warn("port already in use");
  }
});
```
<hr>

### Com Hono (Node.js)
`CommonJS`
```javascript
const { Hono } = require("hono");
const { serve } = require("@hono/node-server");
const { Server } = require("socket.io");

const app = new Hono();

const httpServer = serve({
    fetch: app.fetch,
    port: 3000,
});

const io = new Server(httpServer, {
    /* options */
});

io.on("connection", (socket) => {
    // ...
});
```
<hr>

### Com Hono e Bun

```javascript
import { Server as Engine } from "@socket.io/bun-engine";
import { Server } from "socket.io";
import { Hono } from "hono";

const io = new Server();

const engine = new Engine();

io.bind(engine);

io.on("connection", (socket) => {
  // ...
});

const app = new Hono();

const { websocket } = engine.handler();

export default {
  port: 3000,
  idleTimeout: 30, // deve ser maior que a opção "pingInterval" do engine, cujo valor padrão é 25 segundos

  fetch(req, server) {
    const url = new URL(req.url);

    if (url.pathname === "/socket.io/") {
      return engine.handleRequest(req, server);
    } else {
      return app.fetch(req, server);
    }
  },

  websocket
}
```
