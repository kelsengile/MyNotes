[Previous](./[18]-GraphQL-With-NestJS.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[20]-Microservices-And-Queues.md)

*Advanced Features*

# Lesson 19 - WebSockets And Real-Time

## 19.1 Gateways

HTTP is request and response: the client always asks first. **WebSockets** keep a connection open so the server can also **push** data at any time, which suits chat, live notifications, and dashboards.

In Nest, a **gateway** is a provider that handles WebSocket connections. The default adapter uses **Socket.IO**.

```bash
npm install @nestjs/websockets @nestjs/platform-socket.io
```

```ts
import {
  OnGatewayConnection, OnGatewayDisconnect, SubscribeMessage,
  WebSocketGateway, WebSocketServer, MessageBody, ConnectedSocket,
} from '@nestjs/websockets';
import { Server, Socket } from 'socket.io';

@WebSocketGateway({ cors: { origin: '*' } }) // restrict origin in production
export class ChatGateway implements OnGatewayConnection, OnGatewayDisconnect {
  @WebSocketServer()
  server: Server;

  handleConnection(client: Socket) {
    console.log(`Connected: ${client.id}`);
  }

  handleDisconnect(client: Socket) {
    console.log(`Disconnected: ${client.id}`);
  }

  @SubscribeMessage('message')
  handleMessage(@MessageBody() data: string, @ConnectedSocket() client: Socket) {
    this.server.emit('message', { from: client.id, text: data }); // broadcast to everyone
  }
}
```

Register it as a provider in a module:

```ts
@Module({ providers: [ChatGateway] })
export class ChatModule {}
```

By default the gateway listens on the same port as your HTTP server. A browser client:

```html
<script src="https://cdn.socket.io/4.7.5/socket.io.min.js"></script>
<script>
  const socket = io('http://localhost:3000');
  socket.on('message', (msg) => console.log(msg));
  socket.emit('message', 'Hello!');
</script>
```

---

## 19.2 Events and rooms

A message handler can **return a value**, which is sent back to the sender as an acknowledgement:

```ts
@SubscribeMessage('ping')
ping(@MessageBody() data: any) {
  return { event: 'pong', data }; // emits "pong" to the sender
}
```

**Rooms** are named groups of sockets. They let you send messages to only some clients.

```ts
@SubscribeMessage('join')
join(@MessageBody() room: string, @ConnectedSocket() client: Socket) {
  client.join(room);
  this.server.to(room).emit('system', `${client.id} joined ${room}`);
}

@SubscribeMessage('room-message')
roomMessage(@MessageBody() payload: { room: string; text: string }, @ConnectedSocket() client: Socket) {
  this.server.to(payload.room).emit('message', { from: client.id, text: payload.text });
}

@SubscribeMessage('leave')
leave(@MessageBody() room: string, @ConnectedSocket() client: Socket) {
  client.leave(room);
}
```

Emitting options on a Socket.IO server or socket:

| Call | Sends to |
|---|---|
| `server.emit(...)` | Every connected client |
| `server.to(room).emit(...)` | Everyone in a room |
| `client.emit(...)` | Only that client |
| `client.broadcast.emit(...)` | Everyone except that client |

A gateway can also be used from other services (inject it) to push events when something happens in your app, such as a new order being saved.

Validation works on gateways. Add `@UsePipes(new ValidationPipe())` on the handler with a DTO class.

---

## 19.3 Authentication for sockets

WebSocket connections should be authenticated, usually with the same JWT as your REST API. The client sends the token in the connection handshake:

```ts
// client
const socket = io('http://localhost:3000', {
  auth: { token: accessToken },
});
```

Verify it as soon as the client connects, and disconnect on failure:

```ts
import { JwtService } from '@nestjs/jwt';

@WebSocketGateway()
export class ChatGateway implements OnGatewayConnection {
  constructor(private readonly jwtService: JwtService) {}

  async handleConnection(client: Socket) {
    try {
      const token = client.handshake.auth?.token as string;
      const payload = await this.jwtService.verifyAsync(token);
      client.data.user = payload; // store for later handlers
    } catch {
      client.disconnect(true);
    }
  }

  @SubscribeMessage('message')
  handleMessage(@MessageBody() text: string, @ConnectedSocket() client: Socket) {
    const user = client.data.user;
    this.server.emit('message', { from: user.email, text });
  }
}
```

Also:

- Restrict `cors.origin` to your real frontend.
- Check **authorization** per room (for example, may this user join this room?).
- Do not put the token in the URL query string if you can avoid it, since URLs often get logged.

---

## 19.4 Scaling considerations

One server can hold many thousands of connections, but with **multiple server instances** behind a load balancer you must handle two issues:

1. **Sticky sessions.** Socket.IO may start with HTTP long-polling before upgrading to WebSocket, so a client's requests must reach the same instance. Configure your load balancer for session affinity (or force the WebSocket transport only on the client).
2. **Cross-instance broadcasting.** `server.emit` only reaches clients connected to **that** instance. Use an adapter backed by Redis so all instances share events.

```bash
npm install @socket.io/redis-adapter redis
```

```ts
// redis-io.adapter.ts
import { IoAdapter } from '@nestjs/platform-socket.io';
import { createAdapter } from '@socket.io/redis-adapter';
import { createClient } from 'redis';

export class RedisIoAdapter extends IoAdapter {
  private adapterConstructor: ReturnType<typeof createAdapter>;

  async connectToRedis() {
    const pubClient = createClient({ url: 'redis://localhost:6379' });
    const subClient = pubClient.duplicate();
    await Promise.all([pubClient.connect(), subClient.connect()]);
    this.adapterConstructor = createAdapter(pubClient, subClient);
  }

  createIOServer(port: number, options?: any) {
    const server = super.createIOServer(port, options);
    server.adapter(this.adapterConstructor);
    return server;
  }
}
```

```ts
// main.ts
const app = await NestFactory.create(AppModule);
const redisIoAdapter = new RedisIoAdapter(app);
await redisIoAdapter.connectToRedis();
app.useWebSocketAdapter(redisIoAdapter);
```

Other considerations:

- Limit message size and rate per client to prevent abuse.
- Handle reconnection on the client (Socket.IO does this automatically).
- Clean up room membership and resources in `handleDisconnect`.
- Monitor connection counts and memory usage.

---

[Previous](./[18]-GraphQL-With-NestJS.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[20]-Microservices-And-Queues.md)