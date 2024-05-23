# TwiML Verb: `<Dial>`

@TODO description

@TODO walk through a simple dial scenario, talk how kazoo process the call, makes offnet req and talk about resources!
@TODO talk about if offnet fails what happens, or when a call ends what will happens and all of that


## Attributes

| Name | Values Description | Default value |
|------|--------------------|---------------|
| `hangupOnStar` | | |
| `timeout` | | |
| `timeLimit` | | |
| `action` | | |
| `callerId` | | |
| `method` | | |

@TODO verify these:
| `record` | | |
| `hangupOn` | | |
| `collect_dtmf` | | |
| `continueOnFail` | | |

## Nesting Nouns

@TODO description

| Noun         | Description                                                        |
| ------------ | ------------------------------------------------------------------ |
| plain text | simple dial, will format text as DID will make and offnet call |
| [`<Number>`](./dial/number.md)     | DID with extended attributes, @TODO: will create endpoint and acts like call forward |
| [`<Conference>`](./dial/conference.md) | Conference room endpoint                                |
| [`<User>`](./dial/user.md)       | ID of an existing Kazoo User (works like the User callflow element |
| [`<Device>`](./dial/device.md)     | ID of an existing Kazoo Device                                     |
| [`<Sip>`](./dial/sip.md)        | SIP URI to dial                                                    |

## Examples
