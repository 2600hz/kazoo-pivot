# \<Play>

Play media for callers

## Overview

Pivot will play the {% BRAND_NAME %} Media Resource in the `<Play>` term body. 

For the [Callflow Json](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/README.md) equivalent, see 
[`play`](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/play.md). 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Play>http://bobanddoug.com/sounds/gwn/the_call.wav</Play>
</Response>
```

## Attributes

| Name                          | Values Description                  | Default value  |
|-------------------------------|-------------------------------------|----------------|
| [`loop`](#loop)               | any positive integer including zero | 1              |
| [`terminators`](#terminators) | any dtmf characters                 | `0123456789*#` |

### `loop`

Specifies how may times the audio file to be played to the user. By default the audio will
play once. A value of `0` will plays the audio file indefinitely until either one party disconnects
or the term otherwise completes from a [`terminator`](#terminator).

### `terminators`

When pressed, this key stops playback. `<Record>` will then complete. By default, any DTMF 
character will halt playback.  

## Specifying Media to Play

If `prompt` is set to a valid media URL or handle, Pivot will playback that media to callers 
before disconnecting the line without answering the call. 
See [Media](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/media.md) in {% BRAND_NAME %} for information 
on uploading and using media files in {% BRAND_NAME %}.

## Nesting

Can be nested under [`<Gather>`](./gather.md) to play audio until the caller provides the first 
DTMF.  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Gather>
        <Play>http://www.bobanddoug.com/sounds/gwn/the_call.wav</Play>
    </Gather>
</Response>
```

Terms cannot be nested within `<Play>`.  

