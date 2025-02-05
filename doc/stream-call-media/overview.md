# Stream Call Media

The ongoing call raw audio can be streamed to your server over a WebSocket connection in near real-time.
This gives you ability to process the audio file by your or you chosen 3rd party services. The use cases are
like making an interactive converstional IVR or AI assistant, real-time voice detection or call transcription,
voicemail detection, fraud detection/perevention, voice authentication, and more.

Stream can only be used for an _ongoing_ call.

<!--
TODO: improve this, add callflow stream doc and etc....
Stream provided by Pivot is a bit different from the one provided by [Callflow Stream](https://gothub.com/2600hz/kazoo-callflow/doc/stream.md)
or other ways available in 2600Hz.

something like: The main difference is on the message type, and your application being able to send media to be played back on the call.
-->

A Pivot stream can only be created using Pivot [`<Stream>`](../twiml/stream.md) noun.
Pivot's stream has different types than the regular stream capability provided by 2600Hz Callflow.
This page covers _only_ `<Stream>` Pivot TwiML and its different types. <!-- TODO: For Callfloww stream please see callflow doc -->

## Unidirectional Streams

This is more simpler type, and sends the Call's audio over WebSocket, but this is one way connection and your
application _can not_ send messages back to the Pivot server.

In this stream type, all WebSocket messages are in plain text and are in JSON format. The raw audio is encoded as base64 in
[`media`](./websocket-messages.md#media-message) message.

Any detected DTMF in inbound direction (like callerr presses a touch-tone number)
may also be sent over WebScoket in [`dtmf`](./websocket-messages.md#dtmf-message) message.


Unidirectional stream can be started using `<Start><Stream>` TwiML. When the stream is started, Pivot immediately continues processing
the rest of TwiML document. If no instruction left the call will end. Therefore you should include instructions after the `<Start><Stream>`.

<!-- TODO link to /channels API (and konami_pro stream action??) -->
You can stop the stop by using `<Stop><Stream>` TwiML, or using API, or when the call ends or your application disconnects the WebSocket connection.

<!-- TODO: there is no limitation on which track can be used in bi or uni dorectional, like _other_ services, /end-of-bragging -->
There is a limit of attaching 4 streams to a call in Pivot.

<!-- TODO Provide some example apps? it wou;d be nice to create a repo with some code examples for pivot in general -->

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

Here Pivot establishes the WebSocket connection to the specified url, and streams the call's audio to your application and
uses TTS to say the specified string from `<play>` verb and ends the call and stream afterwards.

## Raw Audio Streams

If you just need raw audio of the call and no need the extra JSON messages in plain text format, you can use [`rawAudio`](../twiml/stream.md#rawaudio)
attribute of `<Stream>`. This type is similiar to [Unidirectional Streams](#unidirectional-streams) in that your applications can not
sned WebSocket message back to Pivot, but instead your applications receives the raw audio in binary format.

Also there is no [`dtmf`](./websocket-messages.md#dtmf-message) messages in this type. Other than this, this type is similiar to Unidirectional Streams.

For example to start a Raw Aduio stream:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Start>
      <Stream name="Example" url="wss://your_application/stream" rawAudio="true" />
  </Start>
  <Say>The stream has started and your applications receives only the raw audio in binary format.</Say>
</Response>
```

## Bidirectional Streams

In bidirectional streams, your applications receives the call's audio stream and addtionally it can also sends
audio streams back to pivot to be played on the call. This enables to create a real-time interactive conversational
call with like an AI agent.

After WebSocket connection is established, the call's audio streams to your application. Your application may choose to stream
some aduio back to Pivot over same WebSocket connection to be played on the call.

Any detected DTMF in inbound direction (like caller presses a touch-tone number)
may also be sent over WebScoket in [`dtmf`](./websocket-messages.md#dtmf-message) message.

Bidirectional stream can be started using `<Connect><Stream>`. After the websocket connection is established, no more instruction from the
TwiML document will be execute, and Pivot blocks until either the call ends or your application disconnect from WebSocket, in either case Pivot will
stop and and the call if it is not already.

You can only have one Pivot bidirectional stream attached to a call.

Example how to start a bidirectional stream:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
 <Connect>
     <Stream url="wss://your_application/ostream" />
 </Connect>
 <Say>This verb will never be exexcuted by Pivot on bidirectional streams.</Say>
</Response>
```

Here Pivot establishes the WebSocket connection to the specified url, and streams the call's audio to your application. The execution of TwiML is blocked
until the stream or the call ends and after that Pivot will stop and hanup the call if it is still up.

The `<Say>` verb will never be executed by Pivot.

<!-- provide a link to an example application (receiveing side of stream not just XML) in the future -->

## Addtional Resources

- [\<Stream> Pivot TwiML Reference](../twiml/stream.md)
- [Pivot Stream WebSocket Messages Reference](./websocket-messages.md)
<!-- - [Callflow Stream](http://github.com/2600hz/kazoo-callflow/doc/stream.md), alternative way to start a stream using onlt Callflow -->
<!-- - [Konami Pro Stream Action](http://github.com/2600hz/kazoo-konami-pro/doc/stream.md) allows you to control a stream via Channels API or Metaflow -->
<!-- link to a example websocket application on how to use pivot stream -->
