# \<Dial>

Connect callers to new telephone endpoints

## Overview

`<Dial>` will connect an active call to another [endpoint](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/endpoints.md). 
[Supported party types](#Nesting-Nouns)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>555-459-0451</Dial>
</Response>
```
Pivot will attempt to dial the offnet number `555-459-0451`. Pivot will not render the next term 
in the document until after the `<Dial>` completes when the callee does not answer or disconnects
the call.  

If the original caller disconnects the call, Pivot will end the session and later terms in the 
document will not be executed.  

Once this Pivot document is hosted, any spare number can be routed to use it through the Pivot 
App.  

If you are using BYOC, make sure Kazoo is configured to use your carrier.  

## Attributes

| Name                                 | Values Description                                                                                                       | Default value    |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------|------------------|
| [`action`](#action)                  | URL                                                                                                                      | none             |
| [`callerId`](#callerId)              | any string                                                                                                               | from caller      |
| [`hangupOnStar`](#hangupOnStar)      | `true` \| `false`                                                                                                        | `false`          |
| [`method`](#method)                  | `GET` \| `POST`                                                                                                          | `GET`            |
| [`timeLimit`](#timeLimit)            | any positive integer (seconds)                                                                                           | 14400 (4 hours)  |
| [`timeout`](#timeout)                | any positive integer (seconds)                                                                                           | [30](#timelimit) |
| [`record`](#record)                  | `record-from-answer`\| `record-from-ringing`\| `record-from-answer-dual`\| `record-from-ringing-dual`\| `true`\| `false` | none             |
| [`recordingUrl`](#recordingUrl)      | URL                                                                                                                      | none             |
| [`recordStoreMethod`](#recordingUrl) | `PUT` \| `POST` \| `PATCH`                                                                                               | `PUT`            |


### `action`

The URL where Pivot will make an HTTP request once the `<Dial>` term completes. 
[`method`](#method) sets the HTTP method of the request.  Pivot expects another Pivot document 
in response as either Kazoo JSON or TwiML, which will be rendered. Pivot never continues 
execution of the current document after an `action`, further terms will be unreachable.  

### `callerId`

The caller ID the callee will see. Can be set to any value. Defaults to the caller's caller ID. 

### `hangupOnStar`

Allows the original caller to hang up on the dialed party without ending the Pivot session by 
pressing the * key. Dial execution completes, including sending requests to the `action` url if 
provided, and Pivot will attempt to execute the next term in the document.  

### `method`

Sets the HTTP request method for the [`action`](#action) request, either `POST` or `GET`. Pivot 
expects a response with more call instructions. See [Requests](./requests.md) for information 
about the request and response formats.  
Defaults to `GET`.  

### `timeLimit`

Maximum call duration, in seconds. After `timeLimit` seconds pivot will automatically hang up the 
call, defaults to 14400 seconds (4 hours).  

### `timeout`

Pivot will wait for the called party to answer for `timeout` seconds. 

Pivot always adds an additional five seconds to `timeout`. for example `timeout` may be 
configured to 0 seconds, but because of this additional buffer, the true minimum time Pivot will 
wait for an answer is 5 seconds. The default value for the `timeout` parameter is 30 seconds, so 
the true default time Pivot waits on an answer by default is 35 seconds.

### `record`

When set, record both legs of a call during the `<Dial>`. Recording can be either mono or stereo. 
Currently conferences cannot be recorded using this attribute.  

in dual channel recording, each leg uses a separate channel in the recording. This is the default across
Kazoo and is elsewhere referred to as stereo recording. There are two options for dual channel 
recording:
- `record-from-answer-dual`, which is equivalent to `record_on_answer` in Callflow and begins recording when
the call is answered 
- `record-from-ringing-dual`, which is equivalent to `record_on_bridge` in Callflow and begins recording
as soon as the line starts ringing

For compatibility, mono recording with Pivot TwiML terms is also available.
- `record-from-answer`, which is equivalent to `record_on_answer` in Callflow and begins recording when
the call is answered. `true` is an alias for `record-from-answer`
- `record-from-ringing`, which is equivalent to `record_on_bridge` in Callflow and begins recording
as soon as the line starts ringing.

Any other value of `record` will not record the call, including `false` and `do-not-record`.  

2600Hz will only store call recordings if the account has an appropriate 
[storage plan](https://docs.2600hz.com/git/2600hz/crossbar/storage.md) setup. 
For accounts without storage for recordings, Pivot can send recordings to a developer server 
using the `recordingUrl` attribute. To retrieve calls stored by 2600Hz, see 
[Kazoo API Call Recordings](https://docs.2600hz.com/git/2600hz/crossbar/recordings.md}).

### `recordingUrl`

Kazoo will attempt to send recordings to this URL plus a generated media name when set. For example, when 
`recordingUrl=myrecordings.com/pivot`, Kazoo will send a recording to `myrecordings.com/pivot/\[mediaName]`

### `recordStoreMethod`

Method to use when sending media to `recordingUrl`.


## Nesting Nouns

Pivot supports several TwiML nesting nouns for <Dial>, allowing callers to be connected to a 
variety of endpoints.  

| Noun                                   | Description                                                                              |
|----------------------------------------|------------------------------------------------------------------------------------------|
| plain text                             | Simple dial. Pivot expects a complete phone number which can be offnet                   |
| [`<Conference>`](./dial/conference.md) | Conference room endpoint                                                                 |
| [`<Device>`](./dial/device.md)         | ID of an existing Kazoo Device                                                           |
| [`<Number>`](./dial/number.md)         | Plain text <Dial> with extended attributes                                               |
| [`<Sip>`](./dial/sip.md)               | SIP URI to dial                                                                          |
| [`<User>`](./dial/user.md)             | ID of an existing Kazoo User, see (Callflow Users)[]                                     |

