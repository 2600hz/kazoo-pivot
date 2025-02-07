# Pivot Stream WebSocket Messages

During both unidirectional and bidirectional Streams, Pivot may send plain text JSON messages over
the Websocket. During a Bidirectional Stream, your server can optionally send plain text JSON 
messages back to Pivot.

During a [`raw`](./overview.md#raw-audio-streams) Stream, Pivot will not send and cannot receive
these messages.


## Websocket messages send by Pivot

When the WebSocket connection is established, Pivot will immediatley sends one of each of `connected` and `start` messages in order to your server.

After those messages your appplication can expect to receive a stream of messages:

- `media`: includes the the Base64 encoded audio of the call.
- `dtmf`: Any detected DTMF on the call.
- `stop`: indicates the and of stream, the stream will end immediatley after this.
- `mark`: In case of bidirectional stream, this indicates the previously request buffered playback media is finished being played on the call.

## Common Properties

Except the `connected` message, other messages send by Pivot to your server will include these common fields:

| Name             | Description                                                              |
|------------------|--------------------------------------------------------------------------|
| `event`          | Indicates the type of message                                            |
| `sequenceNumber` | A number indicating the sending order for keeping track of messages sent |
| `streamSid`      | The unique ID of the stream you use to reference the stream              |

### Connected message

For the first time that Pivot connects to your WebSocket server, it immediately sends this message to information about the connection itself.
This message is send only once at the beginning and won't be sent afterwards again.

| Name             | Description               |
|------------------|---------------------------|
| `event`          | Always set to `connected` |
| `protocol`       | Always set to `Call`      |
| `version`        | Always set to `1.0.0`     |

```json
{
  "event": "connected",
  "protocol": "Call",
  "version": "1.0.0"
}

```

### Start message

Immediately after `connected`, Pivot will send this message with information critical about the stream. This message only send only once during
the time of stream.

| Name                           | Description                                                                                                                                               |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `event`                        | Always set to `start`                                                                                                                                     |
| `sequenceNumber`               | A number indicating the sending order for keeping track of messages sent                                                                                  |
| `start`                        |                                                                                                                                                           |
| `start.accountSid`             | The unique ID of the account the call is associated with                                                                                                  |
| `start.streamSid`              | The uniqeue ID of the stream                                                                                                                              |
| `start.callSid`                | The uniqeue ID of the incoming call                                                                                                                       |
| `start.tracks`                 | Arrays of string which indicates the the type of call's media flow for the stream, possible values are `inbound` and `outbound`                           |
| `start.mediaFormat`            | Object with streams' media format info                                                                                                                    |
| `start.mediaFormat.encoding`   | The audio encoding format of the stream, the value depends on the audio encodign used the the endpoint that initiated the stream, usually `audio/x-mulaw` |
| `start.mediaFormat.sampleRate` | Sample rate of media, as requested when initializing the request by `<Stream>` noun                                                                       |
| `start.mediaFormat.channels`   | The number of audio channels of the stream's media, as request when initializing the stream with `<Stream>`                                               |
| `start.customParameters`       | The custom parameters as-is set on `<Stream>` noun                                                                                                        |
| `streamSid`                    | The unique ID of the stream you use to reference the stream                                                                                               |

```json
{
  "event": "start",
  "sequenceNumber": "1",
  "start": {
    "accountSid": "ACXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "callSid": "CAXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "tracks": [ "inbound" ],
    "mediaFormat": {
        "encoding": "audio/x-mulaw",
        "sampleRate": 8000,
        "channels": 1
    },
    "customParameters": {
     "FirstName": "Jane",
     "LastName": "Doe",
     "RemoteParty": "Bob",
   },
  },
  "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

### Media message

This message included the encoded raw audio of the call. Sends multiple time during life of stream as the audio is received (or sent) to
the endpoint.

| Name              | Description                                                                                                                                                                                 |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `event`           | Indicates the type of message                                                                                                                                                               |
| `media`           | Object containing the media                                                                                                                                                                 |
| `media.track`     | The track type of the media, depending on initial `<Stream>`, see [`track`](../twiml/stream.md#track) and  [`audioMix`](../twiml/stream.md#audiomix)                                        |
| `media.chunk`     | Number to keep track of sent chunk of medias, always start at `1` and increments in subsequent media messages                                                                               |
| `media.timestamp` | The time in seconds since the start of Pivot opened the connection to you server                                                                                                            |
| `media.payload`   | Base64 encoded of the raw audio, as indicated by `track` may include inbound or outbound or both of call's [track](../twiml/stream.md#track), [mixed](../twiml/stream.md#audiomix) together |
| `sequenceNumber`  | A number indicating the sending order for keeping track of messages sent                                                                                                                    |
| `streamSid`       | The unique ID of the stream you use to reference the stream                                                                                                                                 |

```json
{
 "event": "media",
 "sequenceNumber": "3",
 "media": {
   "track": "outbound",
   "chunk": "1",
   "timestamp": "5",
   "payload": "no+JhoaJjpz..."
 } ,
 "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

### Stop message

Sent at the end of stream, indicating the stream is ended before disconnecting.

| Name              | Description                                                              |
|-------------------|--------------------------------------------------------------------------|
| `event`           | Indicates the type of message                                            |
| `stop.accountSid` | The unique ID of the account the call is associated with                 |
| `stop.callSid`    | The uniqeue ID of the incoming call                                      |
| `sequenceNumber`  | A number indicating the sending order for keeping track of messages sent |
| `streamSid`       | The unique ID of the stream you use to reference the stream              |

```json
{
 "event": "stop",
 "sequenceNumber": "5",
 "stop": {
    "accountSid": "ACXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "callSid": "CAXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
  },
  "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

### DTMF message

Sent anytime Pivot received a DTMF from the call's channel that initiated the stream (the inbound track).

| Name             | Description                                                              |
|------------------|--------------------------------------------------------------------------|
| `event`          | Indicates the type of message                                            |
| `dtmf.track`     | Value is awlays set to `inbound_track`                                   |
| `dtmf.digit`     | The detected DTMF number as string                                       |
| `sequenceNumber` | A number indicating the sending order for keeping track of messages sent |
| `streamSid`      | The unique ID of the stream you use to reference the stream              |

```json

{
  "event": "dtmf",
  "streamSid":"MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "sequenceNumber":"5",
  "dtmf": {
      "track":"inbound_track",
      "digit": "1"
  }
}
```

### Mark message


Sent only on __bidirectional Pivot Streams__, indicating the playback media you sent to Pivot is played on the call. See [Playing media on the call](#play-media-on-the-call).


This message will be sent either on:

- When the playback media is finished playing on the call
- Your server [cleared](#clear-the-buffered-media) the playback media buffer
- The stream is ended

| Name             | Description                                                                                              |
|------------------|----------------------------------------------------------------------------------------------------------|
| `event`          | Indicates the type of message                                                                            |
| `sequenceNumber` | A number indicating the sending order for keeping track of messages sent                                 |
| `streamSid`      | The unique ID of the stream you use to reference the stream                                              |
| `mark.name`      | The mark name used by the `mark` message your server sent to Pivot (or set to `unknown` if not provided) |

```json
{
 "event": "mark",
 "sequenceNumber": "4",
 "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
 "mark": {
   "name": "my label"
 }
}
```

## WebSocket message from your server to Pivot

In birdirectional mode stream, your server can send message back to Pivot. These messages are solely for the propuse of playing
some audio back to the call and control the follow of this playback.

- `media`: streams of the playback media, all media messages are buffered until the mark message is sent which then this buffer gets played on the call.
- `mark`: indicates the streaming of playback media is finished, and it should be played on the call.
- `clear`: discard and empty all media and their buffers.

Sending anf of these messages to Pivot on ` unidirectional` if prohibited and Pivot will ignores if received any messages back from your server.

### Play media on the call

<!-- TODO: write me please ^.; overview of low of how to play a media on the call, the message your server send and the message that pivot may send and such -->

For a better interaction with caller, you may send some media to be played on the call. For this propuse you must the Pivot Stream in __bidirectional__ mode.

To initiat the playback media, you start by sending [`media`](#send-a-playback-media-message) to Pivot. You may send as many as media message you need.
Pivot won't play these receive media message chunks, instead it will buffer them. When you are done sending all media needed to be played, you will send
a [`mark`](#send-a-mark-message-to-play-the-media-on-the-call) to Pivot indicating the end of buffered playback media.

Pivot will now immediatly playing the received media on the call.

If you want to cancel the current buffered media for any reason, you may send a [`clear`](#clear-the-buffered-media) message so Pivot will clear all buffered media messages.

If you buffer some media, then send mark message to get them played and immediatly start buffering another media, Pivot will buffer them, but if you issue a `mark`, before you wait for previous
buffered to finsihed playing on the call, the current buffered media won't be played on the call immediatly, instead it will be queued to played after. Any ququed play command will be play in the order
the buffered media mark message is sent.

If you send a `clear` message, any current activly buffering media will be cleared. Curerntly sending a `clear` message after a `mark` message won't cancel the play, even if it is queued.

When sending the first `media` message to start buffering the media, you __MUST__ specify the media encoding format. If not specified, the media message and its
subsequent messages will be ignored entirely. <!-- TODO: actually if any subseuqnet media message have Content type, it will be get buffered, thou earlier messages are ignored -->


### Send a playback media message

<!-- TODO write this, add properties table -->

starts a buffer on pivot, all received media messages are wirtten into that buffer waiting for mark to be played on the call
`format.encoding` is REQUIRED AND MUST be set on the first current playback media, indicating the encoding of media.
support encoding are `audio/mp3` and `audio/wav`.

sample_rate is arbitrary, (media will resmplae if nessacry, same goes for audio channels).

the media must be base64 encoded.

Media message (at the start of a new buffer):

| Name                    | Description                                                                                |
|-------------------------|--------------------------------------------------------------------------------------------|
| `event`                 | Indicates the type of message                                                              |
| `media.payload`         | Base64 encoded of the media to be buffered                                                 |
| `media.format.encoding` | The encoding format of the media, required. Values includes: `audio/mp3`  and `audio/wav`  |
| `streamSid`             | The unique ID of the stream you use to reference the stream                                |

```json
{
  "event": "media",
  "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "media": {
    "payload": "a3242sa...",
    "format": { "encoding": "audio/mp3" }
  }
}
```

Subsequent media messages until before ending the buffer with `mark` (encoding should not be changed fore the current buffer!):

```json
{
  "event": "media",
  "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "media": {
    "payload": "a3242sa..."
  }
}
```

### Send a mark message to play the media on the call

finsihes the buffer and plays the call, waiting until it finishes to send back mark with specified name here indicate the playback is done, (buffer is cleared)
if no mark name is given, it defaults to `unknown`.

| Name             | Description                                                                                          |
|------------------|------------------------------------------------------------------------------------------------------|
| `event`          | Indicates the type of message                                                                        |
| `mark.name`      | Optional, the naming the play media so you can recognized it later when you recevied `mark`  message |
| `streamSid`      | The unique ID of the stream you use to reference the stream                                          |

```json
{
 "event": "mark",
 "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
 "mark": {
   "name": "my label"
 }
}
```

### Clear the buffered media

Clears any buffered media.

If you send a `clear` message, any current activly buffering media will be cleared. Curerntly sending a `clear` message after a `mark` message won't cancel the play, even if it is queued.

| Name             | Description                                                              |
|------------------|--------------------------------------------------------------------------|
| `event`          | Indicates the type of message                                            |
| `streamSid`      | The unique ID of the stream you use to reference the stream              |

```json
{
 "event": "clear",
 "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
}
```
