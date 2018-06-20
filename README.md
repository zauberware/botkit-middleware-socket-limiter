# botkit-middleware-socket-limiter
Botkit middleware for limiting socket traffic

- [Botkit Middleware Socket Limiter](#botkit-middleware-socket-limiter)
    - [Installation](#installation)
    - [What does it do?](#what-does-it-do)
    - [Function Overview](#function-overview)
    - [Usage](#usage)

This middleware plugin for [Botkit](http://howdy.ai/botkit) allows developers to protect their websocket server from attacks.

## Installation

```bash
npm install botkit-middleware-socket-limiter --save
```

## What does it do?
This middleware aims to protect your server that hosts botkit Socketbots from malicious attacks. Websockets are vulnerable to DDoS, that could be triggered by someone trying to open a large amount of websockets, someone trying to send a high frequency of messages over the websocket or someone trying to send too big messages over the websocket. In order to prevent those scenarios, this middleware keeps track of the websocket usage associated with a certain IP.

At least, that is what we want it to do. This project is meant to be work in progress and we are happy to welcome anyone who wants to contribute to it via a pull request. We will describe the way we think the socket limiter middleware could work but if you have a better idea how to realize the functionality, please let us know!


## Function Overview

### [Receive Middleware](https://github.com/howdyai/botkit/blob/master/docs/middleware.md#receive-middleware)

*   `middleware.receive`: analyse the websocket statistics from the message sender. If unusual activity is detected, close websocket and block sender.


## Usage


### Bot Setup with the socket limiter middleware

Set up a Socketbot with Botkit

```javascript
var Botkit = require('botkit');
var controller = Botkit.socketbot({});
```

Open a websocket server

```javascript
controller.openSocketServer(controller.httpserver);
```

And start the brain

```javascript
controller.startTicking();
```

Create a middleware object

```javascript
var socketLimiterMiddleware = require('botkit-middleware-socket-limiter');
```

When your Socketbot receives a message, let is use the socket limiter middleware

```javascript
controller.middleware.receive.use(function(bot, message, next) {
    socketLimiterMiddleware.receive(bot, message, next);
});
```

## Features

### Store
The socket limiter middleware stores current websocket statistics in order to detect malicious behaviour. It stores the IPs with the amount of open websockets, messages per websocket, connection time and message frequency.

A redis store should be a good option for storing the data.

### Options
It is possible to configure the socket limiter middleware and overwrite the default options. Possible options to set on middleware creation are the following:

| Property                      | Required | Default        | Description                                                                                                                                                                                                                          |
| ------------------------| :---------- | :------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| maxMessageSize                          | No        | TBD   | Maximum size a message sent over the socket connection is allowed to have. |
| maxSocketConnections                 | No        | TBD   | Maximum amount of all websocket connections. |
| maxSocketConnectionsPerIP     | No        | TBD   | Maximum allowed amount of socket connections per IP. |
| maxMessageAmountPerMinute | No        | TBD   | Maximum amount of messages that are allowed to be sent in a minute |

You can specifiy those options in an options object and pass it to the middleware on creation.
