# Pivot Stream WebSocket Messages

Pivot Stream may send plain text messages over WebSocket if the type of stream is anything other than `raw`.
This applies to both unidirectional and bidirectional. `raw` stream type only receives the call's raw audio as binary messages.

The plain texts are formated as JSON.

In case of bidierctional stream, your server can optionally send plain text JSON message back to Pivot.

## Websocket messages send by Pivot

When the WebSocket connection is established, Pivot will immediatley sends one of each of `connected` and `start` messages in order to your server.

After those messages your appplication can expect to recieve a stream of messages:

- `media`: includes the the Base64 encoded audio of the call.
- `dtmf`: Any detected DTMF on the call.
- `stop`: indicates the and of stream, the stream will end immediatley after this.
- `mark`: In case of bidirectional stream, this indicates the previously request buffered playback media is finished being played on the call.

### Connected message

<!-- TODO write this, add properties table -->

<!-- First message, indicates the connection is established. sends once. -->

```json
{
  "event": "connected",
  "protocol": "Call",
  "version": "1.0.0"
}

```

### Start message

<!-- TODO write this, add properties table -->

<!-- send message, immedialy after connected, send only once. contains the stream parameters. -->

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

<!-- TODO write this, add properties table -->

<!-- it contains the encoded raw audio -->

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

<!-- TODO write this, add properties table -->

<!-- final message, indicates the strea (or call) is ended. -->

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

<!-- TODO write this, add properties table -->

<!-- contains the dected dtmf, can be used both on uni and bi directional -->

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

<!-- TODO write this, add properties table -->

<!-- indicates the playback media is played on the call. only bidirectional.
say something about interaction: developer will send media to pivot to be buffered, then sends the mark to indicate the media is complete and must be played, pivot plays
then when the play is done it sends this message back to developer.

it also will be sen on stop if there was any non marked playback media buffered, or if dev sends clear message and there is any unfinished/unmarked buffer. -->

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

### Send a playback media message

<!-- TODO write this, add properties table -->

<!-- starts a buffer on pivot, all received media messages are wirtten into that buffer waiting for mark to be played on the call
  `format.encoding` can be set on the first current playback media, indicating the encoding of media. if not set, `audio/wav` will be assumed.
  support encoding are `audio/mp3` and `audio/wav`

  sample_rate is arbitrary, (freeswitch will resmplae if nessacry), same goes for audio channels.

  the media must be base64 encoded.
-->

Media message (at the start of a new buffer):

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

<!-- TODO write this, add properties table -->

<!-- finsihes the buffer and plays the call, waiting until it finishes to send back mark with specified name here indicate the playback is done, (buffer is cleared) -->
<!-- if no mark name is given, it defaults to `mediaPlayed` -->
```json
{
 "event": "mark",
 "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
 "mark": {
   "name": "my label"
 }
}
```

### Clear buffered media

<!-- TODO write this, add properties table -->

```json
{
 "event": "clear",
 "streamSid": "MZXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
}
```
