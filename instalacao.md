# servidor-socket.io
### Instalação (via NPM)

`NPM`
```cmd
npm install socket.io
```
<hr>

### Para instalar uma versão específica:

`NPM`
```cmd
npm install socket.io@version
```
<hr>

### Por padrão, o Socket.IO usa o servidor WebSocket fornecido pelo pacote ws .

Existem dois pacotes opcionais que podem ser instalados juntamente com este pacote. Esses pacotes são complementos binários que melhoram determinadas operações. Binários pré-compilados estão disponíveis para as plataformas mais populares, portanto, você não precisa necessariamente ter um compilador C++ instalado em sua máquina.

`bufferutil` : Permite realizar operações como mascarar e desmascarar de forma eficiente a carga útil de dados dos frames WebSocket.

`utf-8-validate` : Permite verificar de forma eficiente se uma mensagem contém UTF-8 válido, conforme exigido pela especificação.

### Para instalar esses pacotes:

`NPM`
```cmd
npm install --save-optional bufferutil utf-8-validate
```
<hr>

Qualquer implementação de servidor Websocket que exponha a mesma API que ws (notadamente o método handleUpgrade ) pode ser usada.

Por exemplo, você pode usar o pacote eiows , que é um fork do pacote uws (agora obsoleto) :

`NPM`
```cmd
npm install eiows
```
Em seguida, utilize a wsEngineopção:
```javascript
const { Server } = require("socket.io");
const eiows = require("eiows");

const io = new Server(3000, {
  wsEngine: eiows.Server
});
```
Esta implementação "permite, mas não garante" melhorias significativas de desempenho e uso de memória em relação à implementação padrão. Como sempre, recomendamos que você a teste comparando com seu próprio uso.
<hr>

A partir da versão 4.4.0 , um servidor Socket.IO agora pode se conectar a outro µWebSockets.jsservidor.

Instalação:

`NPM`
```cmd
npm install uWebSockets.js@uNetworking/uWebSockets.js#v20.52.0
```

Uso:
```javascript
const { App } = require("uWebSockets.js");
const { Server } = require("socket.io");

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

Uso com 
O `@socket.io/bun-engine` pacote fornece um mecanismo de baixo nível específico para Bun, projetado para aproveitar a velocidade e a escalabilidade do Bun.

### Instalação
`NPM`
```cmd
bun add socket.io @socket.io/bun-engine
```

### Utilização
```javascript
import { Server as Engine } from "@socket.io/bun-engine";
import { Server } from "socket.io";

const io = new Server();

const engine = new Engine({
  path: "/socket.io/",
});

io.bind(engine);

io.on("connection", (socket) => {
  // ...
});

export default {
  port: 3000,
  idleTimeout: 30, // must be greater than the "pingInterval" option of the engine, which defaults to 25 seconds

  ...engine.handler(),
};
```
