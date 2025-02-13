# Pivot Stream Events

During a stream, Pivot will publish some events which your application can subscribe to, using either 
a Websocket through [Blackhole](https://docs.2600hz.com/git/2600hz/kazoo-blackhole/doc/README.md),
to receive them in real-time, or through 
[WebHooks](https://docs.2600hz.com/git/2600hz/kazoo-webhooks/doc/README.md) to receive them via HTTP request.

All Pivot stream events are in the `Pivot` event category.

## Stream Start

Once the WebSocket is properly established between Pivot and your server, this event fires. The event includes
the unique ID generated for this stream and if configured, the custom name for the stream. If no name was given 
Pivot will generate a unique name.

```json
{
  "Account-ID": "ACXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Call-ID": "CAXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Stream-ID": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Stream-Name": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Event-Cateogry": "pivot",
  "Event-Name": "stream_start",
}
```

## Stream Stop

`stream_stop` fires when the stream ends. This event may include the reason for the stop
and, if applicable, any error that caused the stream to end.

- `Stream-ID` or `Stream-Name` maybe not present if stream failed or stopped before initialization.
- Because of the asynchronous nature of stream, the `Reason` supplied for the stop can be incorrect.
This property is only for general information
- If `Error-Message` is present that means the stream is ended because of an error. The message
may be helpful determining why the stream failed.

Example of a normal ending of a stream:

```json
{
  "Account-ID": "ACXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Call-ID": "CAXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Stream-ID": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Stream-Name": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Reason": "hangup",
  "Event-Cateogry": "pivot",
  "Event-Name": "stream_stop",
}
```

Example of an abnormal ending os a stream:

```json
{
  "Account-ID": "ACXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Call-ID": "CAXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Stream-ID": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Stream-Name": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Reason": "error",
  "Error-Message": "max unidirectional limit reached",
  "Event-Cateogry": "pivot",
  "Event-Name": "stream_stop",
}
```
