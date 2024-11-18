# \<Stream>

Stream noun instructs the Pivot to stream an on-going call's audio to your server over a WebSocket connection
in real-time.


## Overview

\<Stream> let you access the raw audio of a call, for further processing by your application.
The Audio is encoded in `appplication/x-mulaw` (commonly known as PCMU or G.711u) format encoding.

See [Stream Call Media](../stream-call-media/overview.md) to learn more about Pivot's stream and its
messages. This page is reference for usage `<Stream>` noun.

Pivot TwiML supports multiple ways create media stream:

1. Using `<Start><Stream>` to create a unidirectional Stream, which only sends audio to
your server without allowing your application to send messages back over websocket. The messages
are in JSON format with raw audio encoded as Base64. After the stream started, Pivot will immediately
continues to process the rest of TwiML document, if there are any, otherwise it ends the call.
2. In unidirectional stream, it is possible to receive only raw aduio as binary, by setting
 `<Start><Stream rawAudio="true">`.
3. If your application needs to play media back on the call, `<Connect><Stream>` allows you
to create a bidirectional Stream. Here after the stream started, Pivot will abondon the execution of
the rest of TwiML document and waits for either the call or stream to ends. It stops and
hangup the call if it is not already hungup.


Here is a simple example of creating unidirectional stream:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Start>
      <Stream name="Example" url="wss://your_application/stream" />
  </Start>
  <Say>The stream has started.</Say>
</Response>
```

This instruct Pivot to send the current call audio in near real-time to your WebSocket server
at `wss://your_application/stream` address.

Be sure to include other instruction after an unidirectional stream since it will continues
immediately to process the the rest of TwiML document. Here `<Say>` verb is used.
After TwiML document is executed, the call will end and stream will be stopped.

<!-- TODO link to /channels API (and konami_pro stream action??) -->
The stream will end either by using `<Stop><Stream>` in case of unidirectional stream or when call is ended, or using API.

Next example will show how to start a bidirectional stream:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
 <Connect>
     <Stream url="wss://your_application/ostream" />
 </Connect>
 <Say>This verb will never be exexcuted by Pivot on bidirectional streams.</Say>
</Response>
```

<!-- TODO link to /channels API (and konami_pro stream action??) -->
In this example, the stream is started in bidirectional mode and your application can send playback media to Pivot
over same WebSocket connection to be played on the call. The only way to stop the stream is either using API,
or if the call is hungup or your websocket server disconnects.

## Attributes

`<Start><Stream>` noun has the following attributes:

| Name                        | Values Description                                                                                                 | Default value                 |
|-----------------------------|--------------------------------------------------------------------------------------------------------------------|-------------------------------|
| [`url`](#url)               | Optional, a relative or absolute URL to stream the audio to                                                        | Default to TwiML document url |
| [`audioMix`](#audioMix)     | Specifies the audio channel of the stream. Optional.                                                               | `mono`                        |
| [`id`](#id)                 | Only when using `<Stop><Stream>`, used to identify the attached stream to stop by its unique stream ID. Optional.  | none                          |
| [`name`](#name)             | Your custom unique friendly name to identify the stream. Optional.                                                 | Randomly generated            |
| [`rawAudio`](#rawAudio)     | Specifies to send the raw audio instead of encoded as Base64 in a JSON format. Optional.                           | `false`                       |
| [`sampleRate`](#sampleRate) | Specifies the audio sample rate of the stream. Optional.                                                           | `8000`                        |
| [`track`](#track)           | Specfies what side of the call's audio to stream. Optional.                                                        | `inbound_track`               |


### `url`

This is the URL that Pivot uses to establish the WebSocket connection and stream the audio. This can be an absolute or relative URL.
The relative value will be relative to the current TwiML document being processed. If missing the curent TwiML document URL will be used.

If relative or missing, a secure WebSocket URL (`wss`) will be assumed. We highly recommend to use secure `wss` when using absolute URL.

Any string query and fragment will discarded from the URL. See [Sending Custom Parameters](#sending-custom-parameters) on how to include extra custom parameters instead.

### `audioMix`

This attribute along [`track`](#track) atribute can be used to control how the audio that is being trasmitted to your server must be encoded.

Available options are: `mono` and `stereo`.

When `mono`, the stream's audio is on a single channel, regardless of the value of [`track`](#track).

When `stereo`, the stream's audio will on 2 Channels. Depend on the value of [`track`](#track), outbound track will be on the
left channel and inbound track will be on the right channel.

### `id`

Each stream will have a unique identifier, generated by 2600Hz. Pivot will be provide this ID over WebSocket to your application
which then can be used to stop the stream.

<!-- TODO link to /channels API (and konami_pro stream action??) -->
This attribute *must* be used only with `<Stop><Stream>` or when using API to stop the stream. If both [`name`](#name) and `id` is specified, only `id` will
used to find the stream.

When no `id` or [`name`](#name) is provided, `<Stop><Stream>` will stop all streams attached to this call.

See [When a stream stops](#when-a-stream-stops) on how to stop the stream.

### `name`

This is an optional attribute you can use to give the stream started by your application a unique name. You may use this name or the stream [`id`](#id)
generated by 2600Hz to stop the the stream.

The name must be unique on the current call.

If name is missing a random text will be generated and used. The name will be send over in `start` message.

When no `id` or [`name`](#name) is provided, `<stop><Stream>` will stop all streams attached to this call.

### `rawAudio`

Unidirectional stream will be created using `<Start><Stream>` can have this optional attribute to request that Pivot must send the
raw call's audio in binary instead of encoding in Base64 format and sent in a JSON format message.

This option only available in unidirectional stream and when used no [`dtmf`](../stream-call-media/websocket-messages.md#dtmf) message will be sent.
See [Raw Audio Streams](../stream-call-media/overview.md#raw-audio-streams) for further information.

### `sampleRate`

Specifies what sample rate to use to encode the audio of the stream. The value must be divisable by `8000`,
like `8000`, `16000`.

You may use short value of `8k` or `16k`. Defaults to `8000`.

### `track`

Specifies which track of the call to be streamed. Accepted values are: `inbound_track`, `outbound_track`
and `both_tracks`. Default is `inbound_track`.

Track is property of the call, and means where the audio in a given Call is originated from. These origin are
from 2600Hz's perspective.

If the audio that is being transmitted is from the caller (like a caller is speaking into their phone) that audio is
_inbound_ into 2600Hz. If the 2600Hz is transmitting the audio to the caller (like when playing a beep, a media,
a TTS or the audio from the callee's device), then this is _outbound_ track.

When using `both_tracks` you will receive both inbound and outbound tracks.

When `track` and [`audioMix`](#audioMix) used together, how the track sounds are combined together depends on the `audioMix` value:

- `mono`: The combined tracks are on 1 audio channel.
- `stereo`: Audio has 2 channels, with inbound track on the right channel and outbound track on the left channel.

## Nesting

`<Stream>` only accepts `Parameter` as nesting nouns.

`<Stream>` can only be nested under `<Start>`, `<Stop>` and `<Connect>`.

### Sending Custom Parameters

On stream start, it is possible to define some custom parameters to be send to your server over WebSocket.

For example:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Start>
      <Stream name="Example" url="wss://your_application/stream">
        <Parameter name="caller_name" value="Jane Doe" />
        <Parameter name="caller_number" value="+18005550199" />
      </Stream>
  </Start>
  <Say>The stream has started.</Say>
</Response>
```

## When a stream stops

There a different ways when a stream can be stopped:

<!-- TODO link to /channels API (and konami_pro stream action??) -->
1. An error is occured during start of stream, or while stream was in progress.
2. The call that stream is started on has beend ended.
3. Stop is requested via an API call.
4. `<Stop><Stream>` is used in a unidirectional stream
4. Your application ended the WebSocket connection

When no `id` or [`name`](#name) is provided, `<stop><Stream>` will stop all streams attached to this call.

For example in a unidirectional stream, you may start and stop the the stream by:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Start>
      <Stream name="Example" url="wss://your_application/stream" />
  </Start>
  <Say>The stream has started.</Say>
  <Stop>
      <Stream name="Example" url="wss://your_application/stream" />
  </Stop>
  <Play>https://awesome.cool/happy.mp3</Play>
</Response>
```

Since processing of TwiML immediately continues after stream is started, the WebSocket will receive the audio for the `<Say>` verb
and not the audio from `<Play>` verb.
