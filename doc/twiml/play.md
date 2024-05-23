# TwiML Verb: `<Play>`

@TODO description
@TODO explain how people can use Media API to upload media files to be used here
@TODO explain how freeswitch/kazoo will cache the media (find out expiration time)
@TODO epxlain supported media type

## Attributes

| Name | Values Description | Default value |
|------|--------------------|---------------|
| [`loop`](#loop) | integer >=1 | 1 |

@TODO verify this
| [`terminators`](#terminators) | | |


### `loop`

Specifies how may times the audio file to be played to the user. By default the audio will
play once. A value of `0` will plays the audio file indefinitly until either the caller
hangup or if a terminator was pressed by the user if used alongside the `terminators` attributes.


## Specifing the media to play

@TODO describe what play conetnt can be: http/https, media ID, system prompt, silence (advanced options like tone, say and etc...)
@TODO link to `say.md` about say: and TTS

## NEsting

can only be nest under [`<Gather>`](./gather.md). You can't nest anything within this verb.

## Examples
