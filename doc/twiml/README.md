# Pivot TwiML Response

Pivot supports a subset of TwiML to help ease you into Pivot with an existing TwiML-based application.

@TODO a bit decription on how twiml translator works, its culprits, differences to actual twiml, general erros it may returns

## Core Supported


### TwiML Verbs

| Verb     | Description                                              | Nestable Verbs and Nouns                                          |
| -------- | -------------------------------------------------------- | ----------------------------------------------------------------- |
| <Dial>   | Connect the caller to other endpoints                    | plain text DID, <Conference>, <Queue>, <Number>, <User>, <Device> |
| <Record> | Record the caller                                        |                                                                   |
| <Gather> | Collect DTMFs from the caller                            | <Play>, <Say>                                                     |
| <Play>   | Play a media file (mp3, wav) to the caller               |                                                                   |
| Say      | Use a TTS engine to say the supplied text                |                                                                   |
| Redirect | Like an HTTP Redirect, make another HTTP request         | <Variable>                                                        |
| Pause    | Pause callflow execution for supplied number of seconds  |                                                                   |
| Hangup   | Hangup the caller                                        |                                                                   |
| Reject   | Reject (and don't answer - won't start billing) the call |                                                                   |

### Custom Verbs

| Verb  | Description | Nestable Nouns |
| ----  | ----------- | -------------- |
| <Set> | Key value pair(s) to store along-side the call||

### Core TwiML Nouns

| Noun         | Description                                                        |
| ------------ | ------------------------------------------------------------------ |
| <Conference> | Conference room endpoint for <Dial>                                |
| <Queue>      | Call queue to line callers up in                                   |
| <Number>     | DID with extended attributes                                       |
| <User>       | ID of an existing Kazoo User (works like the User callflow element |
| <Device>     | ID of an existing Kazoo Device                                     |
| <Sip>        | SIP URI to dial                                                    |

### Custom Nouns

| Noun       | Description                                                                   |
| ---------- | ----------------------------------------------------------------------------- |
| <Variable> | Includes 'key' and 'value' attributes; values will be put subsequent requests |

## Using Pivot

1. Create a Pivot callflow to point to your webserver URL
2. When a call is placed to the Pivot callflow, your webserver will receive a request
3. Generate a response, using your language of choice, in a supported format
    1. See [sample](https://github.com/2600hz/kazoo/tree/master/applications/pivot/priv/samples) for some example PHP scripts
4. Marvel at how easy that was
