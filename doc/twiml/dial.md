# TwiML Verb: `<Dial>`

@TODO description

@TODO walk through a simple dial scenario, talk how kazoo process the call, makes offnet req and talk about resources!
@TODO talk about if offnet fails what happens, or when a call ends what will happens and all of that


## Attributes

| Name | Values Description | Default value |
|------|--------------------|---------------|
| [`action`](#action)| | |
| [`callerId`](#callerId)| | |
| [`hangupOnStar`](#hangupOnStar)| | |
| [`method`](#method)| | |
| [`timeLimit`](#timeLimit)| | |
| [`timeout`](#timeout)| | |

@TODO verify these:
| [`collect_dtmf`](#collect_dtmf)| | |
| [`continueOnFail`](#continueOnFail)| | |
| [`hangupOn`](#hangupOn)| | |
| [`record`](#record)| | |

### `action`
@TODO

### `callerId`
@TODO

### `hangupOnStar`
@TODO

### `method`
@TODO

### `timeLimit`
@TODO

### `timeout`
@TODO

## Nesting Nouns

@TODO description

| Noun         | Description                                                        |
| ------------ | ------------------------------------------------------------------ |
| plain text | simple dial, will format text as DID will make and offnet call |
| [`<Conference>`](./dial/conference.md) | Conference room endpoint                                |
| [`<Device>`](./dial/device.md)     | ID of an existing Kazoo Device                                     |
| [`<Number>`](./dial/number.md)     | DID with extended attributes, @TODO: will create endpoint and acts like call forward |
| [`<Sip>`](./dial/sip.md)        | SIP URI to dial                                                    |
| [`<User>`](./dial/user.md)       | ID of an existing Kazoo User (works like the User callflow element |

## Examples
