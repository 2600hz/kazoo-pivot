# Pivot Stream Events

Pivot will publishes some events regarding the streams created by Pivot that you can subscribe to receive.

You are able to subscribe to these events either using Websocket (through BlackHole application) for receiving these in real-time or
via WebHooks application to received them via a HTTP request to your server.

All Pivot stream events are in `pivot` category of events.

## Stream Start

When the WebSocket is properly established between Pivot and your server, this event will be fired. The event includes
the unique ID generated for this stream and the unique name you requested for the stream. If no name was given to Pivot
a unique name will begenrated for you.

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

`stream_stop` will indicated the stream is stopped. This event may include the possible reason of stop and
any potentional error cause the stream to end.

- `Stream-ID` or `Stream-Name` maybe not present, if stream failed or stopped before initialization.
- Do not depend on the the value of `Reason`, as it is arbitrary and beacuse of asychronous nature of stream may
indicates different value. This property is onlt for general information.
- If `Error-Message` present that means the stream is ended beacuse of an error. You may look into value of this property to
see some information about why the stream failed.

Example for a normal ending of a stream:

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

Example for abnormal ending os a stream:

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
