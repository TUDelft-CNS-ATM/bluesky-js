# bluesky-js
Bluesky-JS is a client-side JavaScript library for interacting with a BlueSky simulation server using WebSockets.

## Installation
BlueSky-js is available on NPM. You can install it as dependency in your project using:
```
npm install bluesky
```

You can also import it directly with a `<script>` tag in HTML:
```html
<script src="https://unpkg.com/@tudelft/bluesky@latest/dist/bluesky.js"></script>
```
This makes `bluesky` available in the global namespace, exposing `bluesky.client` and `bluesky.stack`.

## Example usage:
### Javascript module
```javascript
import { client, stack } from '@tudelft/bluesky';

// Connect to server
client.connect('localhost', { port: 8080 });

// Subscribe to a topic
client.subscribe('mytopic', (event) => {
  console.log('Received:', event.data);
});

// Register a command
stack.command('greet', (name) => {
  console.log(`Hello, ${name}!`);
});

// Execute a command
stack.stack('greet Alice');
```

### HTML
```html
<script src="https://unpkg.com/@tudelft/bluesky@latest/dist/bluesky.js"></script>

<script>
// Connect to server
bluesky.client.connect('localhost', { port: 8080 });

// Subscribe to a topic, and log data to console when received
bluesky.client.subscribe('mytopic', (event) => {
  console.log('Received:', event.data);
});

// Register a new stack command
bluesky.stack.command('greet', (name) => {
  console.log(`Hello, ${name}!`);
});

// Execute a command
bluesky.stack.stack('greet Alice');
</script>
```


## Client
File: client.js

The Client class manages the WebSocket connection, subscriptions, and shared state synchronization.

### Key Methods
**connect(hostname, options)**: Connects to the WebSocket server.
- hostname: Server address (default: '127.0.0.1')
- options: { port: 8080, clientId: null }

**setActNode(nodeId)**: Sets the active simulation node.
- nodeId: Network ID of the simulation node to set as active

**isConnected()**: Returns true if the WebSocket is open.

**send(topic, data, toGroup)**: Sends a message to the server.
- topic: Topic of the message to send. Nodes that want to receive this message can subscribe to this topic.
- data: data to send.
- toGroup: Group mask to send data to (optional)

**subscribe(topic, func, broadcast, raw, actonly, fromGroup, toGroup)**: Subscribes to a topic.
- topic: Topic of the message to subscribe to
- func: The function to be called when data of this topic is received.
- broadcast: When set to false, only messages explicitly directed at this client will be received.
- raw: In case of sharedstate messages, receive the pre-processed data in the callback function.
- actonly: Only receive broadcast data for the current active node.
- fromGroup: Sender mask for subscription
- toGroup: Destination mask for subscription

**unsubscribe(topic, fromGroup, toGroup)**: Unsubscribes from a topic.
- topic: Topic of the message to unsubscribe from
- fromGroup: Sender mask for subscription
- toGroup: Destination mask for subscription

## Stack
File: stack.js

The Stack class manages command parsing and forwarding.

### Key Methods
**stack(cmdline)**: Parses and executes or forwards a command. 
When a command doesn't exist within this client, the command is forwarded to the active node.
- cmdline: The commandline string to process or send on.

**forward(cmdline, targetId)**: Forwards a command to another node.
- cmdline: The commandline string to forward.
- targetId: Network ID of the node to send the command to.

**command(name, func, options)**: Registers a new command.
- name: Name of the new command
- func: Function to call when this command is parsed.

Command Options
- brief: Short description.
- aliases: Alternative names of the command.
- annotations: Types of each argument.
- help: Specify a help text for the new command.
