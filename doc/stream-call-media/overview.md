# Stream Call Media

The ongoing call audio can be streamed to your server over a WebSocket connection in near real-time.
This gives you ability to process the audio file by your or any 3rd party services. Stream can be used for
an interactive conversational IVR or AI assistant, real-time voice detection or call transcription,
voicemail detection, fraud detection/prevention, voice authentication, and more.

Stream can only be used for an _ongoing_ call.

<!--
TODO: improve this, add callflow stream doc and etc....
Stream provided by Pivot is a bit different from the one provided by [Callflow Stream](https://gothub.com/2600hz/kazoo-callflow/doc/stream.md)
or other ways available in 2600Hz.

something like: The main difference is on the message type, and your application being able to send media to be played back on the call.
-->

A Pivot stream can only be created using the [`<Stream>`](../twiml/stream.md) term.
Pivot's stream has a different interface than the stream capability provided by {% BRAND_NAME %} Callflow.
This page covers _only_ `<Stream>` Pivot TwiML and its different types. <!-- TODO: For Callfloww stream please see callflow doc -->

## Unidirectional Streams

This simpler type of stream sends the Call's audio over WebSocket, but does not allow your 
application to send messages back through the socket.

In this stream type, all WebSocket messages are in plain text and are in JSON format. 
The raw audio is encoded as base64 in a [`media`](./websocket-messages.md#media-message) message.

Any detected inbound DTMF (like caller presses a touch-tone number)
may also be sent over the WebScoket in a [`dtmf`](./websocket-messages.md#dtmf-message) message.

Unidirectional streams are started `<Start><Stream>` TwiML. Once the stream starts, Pivot 
immediately continues processing the rest of TwiML document. If no instruction are left the call 
will end. Therefore you should include instructions after `<Start><Stream>`.

<!-- TODO link to /channels API (and konami_pro stream action??) -->
You can stop the stream by using `<Stop><Stream>` TwiML or using API. The stream automatically 
closes when when the call ends or your application disconnects the WebSocket connection.

<!-- TODO: there is no limitation on which track can be used in bi or uni directional, like _other_ services, /end-of-bragging -->
There is a limit of attaching 4 streams to a call in Pivot.

<!-- TODO Provide some example apps? it wou;d be nice to create a repo with some code examples for pivot in general -->

Here is a simple example of a unidirectional stream:

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
uses TTS to say the specified string from `<Play>` verb and ends the call and stream afterwards.

## Raw Audio Streams

In a [`rawAudio`](../twiml/stream.md#rawaudio) stream, messages are sent as binary audio
without the extra JSON audio. There will not be [`dtmf`](./websocket-messages.md#dtmf-message)
messages in a raw stream. Otherwise, raw streams are similar to Unidirectional Streams, including
that your application will be unable to send messages back to pivot.

For example, to start a Raw Audio stream:

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

In bidirectional streams, your application can send audio streams back to Pivot which are played
on the call. This enables real-time interactivity with your application. For example, a 
bidirectional stream can be used to connect a call to a conversational AI assistant.

Once the connection is established and Pivot is streaming, your application can use the
same WebSocket to send audio back to Pivot, which will be played on the call.

Any detected inbound DTMF (when the caller presses a touch-tone number)
will also be sent over WebSocket in [`dtmf`](./websocket-messages.md#dtmf-message) message.

Bidirectional stream are started using `<Connect><Stream>`. Once the connection is established,
Pivot will abandon the execution of the TwiML document and end the call when the stream ends or 
a party hangs up. When your application disconnects from the WebSocket, Pivot will immediately
end the call.

A call can only have one Pivot bidirectional stream attached.

Example how to start a bidirectional stream:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
 <Connect>
     <Stream url="wss://your_application/ostream" />
 </Connect>
 <Say>This verb will never execute after a bidirectional stream.</Say>
</Response>
```

Here Pivot establishes the WebSocket connection to the specified url, and streams the call's 
audio to your application. Once the stream closes or the caller hangs up, Pivot will end the
call. The `<Say>` verb will never be executed by Pivot.

<!-- provide a link to an example application (receiveing side of stream not just XML) in the future -->

## Current Limitations

The stream is encoded using the codec of the call that initiated the stream. Currently Pivot
cannot re-encode or communicate to your application what codec is used.

## Addtional Resources

- [\<Stream> Pivot TwiML Reference](../twiml/stream.md)
- [Pivot Stream WebSocket Messages Reference](./websocket-messages.md)
<!-- - [Callflow Stream](http://github.com/2600hz/kazoo-callflow/doc/stream.md), alternative way to start a stream using onlt Callflow -->
<!-- - [Konami Pro Stream Action](http://github.com/2600hz/kazoo-konami-pro/doc/stream.md) allows you to control a stream via Channels API or Metaflow -->
<!-- link to a example websocket application on how to use pivot stream -->
