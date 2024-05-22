# Pivot TwiML Translator

Pivot supports a subset of TwiML to help ease you into Pivot with an existing TwiML-based application.

To use this translator your application must respond to Pivot request with `Content-Type:
application/xml` HTTP header and a valud TwiML document.

@TODO a bit decription on how twiml translator works, its culprits or differences to actual twiml if any, general erros it may returns if any

TwiML is an XML-based with only one root XML element of `<Response>`. The TwiML must start
with XML declaration.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <!-- your instructions goes here -->
</Response>
```

For example to play a media with ID `ad303920d65b486f1ba0e2c3fe274418` from your account
you can response like:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Play>ad303920d65b486f1ba0e2c3fe274418</Play>
</Response>
```

In TwiML elements under `Response` are called **verbs**. Pivot supports popular verbs and
tries to behave as much as possible to the original behavior as much as possible. Verbs
can have noun elements under them. Please refer to each verbs documentation to learn more about them.

Verbs and nounds are processed from top to bottom. Some verbs or nouns may cause the rest
of verbs or nouns after them to be not reachable, such as `<Hangup>`.

## Pivot TwiML Verbs

@TODO some fancy desciption or filler

| Verb     | Description                                              | Nestable Verbs and Nouns                                          |
| -------- | -------------------------------------------------------- | ----------------------------------------------------------------- |
| `<Dial>`   | Connect the caller to other endpoints                    | plain text DID, `<Conference>`, `<Queue>`, `<Number>`, `<User>`, `<Device>` |
| `<Record>` | Record the caller                                        |                                                                   |
| `<Gather>` | Collect DTMFs from the caller                            | `<Play>`, `<Say>`                                                     |
| `<Play>`   | Play a media file (mp3, wav) to the caller               |                                                                   |
| `<Say>`      | Use a TTS engine to say the supplied text                |                                                                   |
| `<Redirect>` | Like an HTTP Redirect, make another HTTP request         | `<Variable>`                                                        |
| `<Pause>`    | Pause callflow execution for supplied number of seconds  |                                                                   |
| `<Hangup>`   | Hangup the caller                                        |                                                                   |
| `<Reject>`   | Reject (and don't answer - won't start billing) the call |                                                                   |
| `<Set>` | Key value pair(s) to store along-side the call| |

