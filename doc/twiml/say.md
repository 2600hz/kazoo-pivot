# \<Say>

Use a text-to-speech engine in calls  

## Overview 

Pivot will use a TTS engine to say text in the body of the term.
To use `<Say>`, the {% BRAND_NAME %} account must have a TTS engine configured. Currently, {% BRAND_NAME %} supports 
iSpeech, Flite, Google TTS, and, Voice Fabric. To add iSpeech to a {% BRAND_NAME %} account, follow 
[Add iSpeech TTS](https://docs.2600hz.com/git/2600hz/docs-sysadmin/doc/config/adding-ispeech-tts.md) tutorial.
{% BRAND_NAME %} ships with Flite configured for use out of the box, though individual installations may be 
configured differently.

For the [Callflow Json](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/README.md) equivalent, see [`tts`](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/tts.md).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>Pivot is pretty awesome, Have a nice day</Say>
</Response>
```

## Attributes

| Name                          | Values Description                                                | Default value                       |
|-------------------------------|-------------------------------------------------------------------|-------------------------------------|
| [`loop`](#loop)               | any positive integer including zero                               | 1                                   |
| [`terminators`](#terminators) | any dtmf characters                                               | `0123456789*#`                      |
| [`language`](#language)       | any language supported by the TTS engine                          | account default or `en-us`          |
| [`voice`](#voice)             | any voices supported by the TTS engine, usually `male` or `female | account default TTS voice or `male` |
| [`engine`](#engine)           | any engine configured in the {% BRAND_NAME %} account             | account default                     |

### `loop`

Specifies how may times the body text is said to the user. By default the text will
play once. A value of `0` will loop the text indefinitely until either one party disconnects
or the term otherwise completes from a [`terminator`](#terminator).  

### `terminator`

When pressed, this key stops playback. `<Say>` will then complete. By default, any DTMF 
character will halt playback.  

### `language`

Check the TTS engine for available languages.  
If unspecified, Pivot will use to the default TTS language for the {% BRAND_NAME %} account. If no account
default is configured, Pivot defaults to `en-us`.  

### `voice`


Check the TTS engine for available voices, most support `male` and `female`.  
If unspecified, Pivot will use to the default `voice` for the {% BRAND_NAME %} account. If no account
default is configured, Pivot defaults to `male`.  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say voice="woman" language="en-gb" loop="2">Hello</Say>
</Response>
```
### `engine`

Select from configured TTS engines available to the {% BRAND_NAME %} account. Each `<Say>` term can use
a different engine, if desired.  

## Nesting

Can be nested under [`<Gather>`](./gather.md) to play TTS until the caller provides the first 
DTMF.  
Terms cannot be nested within `<Say>`.  
