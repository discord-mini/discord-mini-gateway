# discord-mini-gateway

This is a small package that can be used to maintain a gateway connection with Discord.

* [Optional Dependencies](#optional)
* [Quick Start](#quickstart)
* [More Details](#details)
* [Sharding](#sharding)

## optional
There are a few optional dependencies which can be used to speed up your connection.
1. [erlpack](https://github.com/discordapp/erlpack) for faster encoding/decoding
2. [bufferutil](https://www.npmjs.com/package/bufferutil) for a faster websocket connection
3. [utf-8-validate](https://github.com/websockets/utf-8-validate) check if a buffer contains valid UTF-8 text quickly

## quickstart
```
npm install rei2hu/discord-mini-gateway
```
or
```
yarn add https://github.com/rei2hu/discord-mini-gateway
```

Here is how to create a connection to the gateway.

```js
const Gateway = require('gateway');
const socket = Gateway(token);

socket.connect();

socket.on('MESSAGE_CREATE', (shard, data) => {
  console.log(shard, data.content);
});
```

The socket will emit events identical to the payload's `t` key. The listener will have two parameters, `shard` and `data` 
where `shard` is the shard that recieved the event and `data` is the payload's `d` key.

More information about the structure and format of payloads can be found on [Discord's 
official documentation](https://discordapp.com/developers/docs/topics/gateway#commands-and-events-gateway-events).

## details

There are a few other events you might be interested in listening to

##### DEBUG

```js
socket.on('DEBUG', (shard, message) => {
  console.log(message);
});
```

The `DEBUG` event is emitted for heartbeats, connects, disconnects, and reconnects. It's just there so you can have a look at
the socket under the hood.

##### PAYLOAD

```js
socket.on('PAYLOAD', (shard, payload) => {
  console.log(shard, payload.op);
});
```

The `PAYLOAD` event is there in case you want to handle payloads yourself and need the entire thing instead of just the `d` key.

#### Sending

You can also send information to Discord through the socket
```js
const i = 0;
// an example of a presence update packet
socket.send({
  op: 3,
  d: {
    status: 'online',
    afk: false,
    since: null,
    game: {
      name: 'shard ' + i,
      type: 0
    }
  }
}, i);
```

The second paramter determines which shard to send to and is by default 0. This way, you can send different payloads to
different connections.

## sharding

This will handle sharding if necessary. Just pass in a number when you create the socket or else the package will use
Discord's suggested number of shards.

```js
// 4 shards
const socket = Gateway(token, 4);
```

And that's about all you have to do. Remember that all events are emitted with a shard argument so you can determine which shard
recieved what event.
