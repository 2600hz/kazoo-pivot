# \<Dial>
Connect callers to new telephone endpoints

## Overview
`<Dial>` will connect an active call to another [endpoint](applications/crossbar/doc/endpoints.md). [Supported party types](#Nesting-Nouns)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>555-459-0451</Dial>
</Response>
```
Pivot will attempt to dial the offnet number `555-459-0451`. Pivot will not render the next term 
in the document until after the `<Dial>` completes when
-  the callee disconnects the call
-  the the callee does not answer

If the original caller disconnects the call, Pivot will end the session and later terms in the 
document will not be executed.  

Once this Pivot document is hosted, any spare number can be routed to use it through the Pivot 
App.  

If you are using BYOC, make sure Kazoo is configured to use your carrier.  

## Attributes

| Name                            | Values Description             | Default value     |
|---------------------------------|--------------------------------|-------------------|
| [`action`](#action)             | URL                            | none              |
| [`callerId`](#callerId)         | any string                     | from caller       |
| [`hangupOnStar`](#hangupOnStar) | `true` \| `false`              | `false`           |
| [`method`](#method)             | `GET` \| `POST`                | `GET`             |
| [`timeLimit`](#timeLimit)       | any positive integer (seconds) | 14400 (4 hours)   |
| [`timeout`](#timeout)           | any positive integer (seconds) | [30*](#timelimit) |



### `action`
The URL where Pivot will make an http request once the `<Dial>` term completes. 
(`method`)[#method] sets the http method of the request.  Pivot expects another Pivot document 
in response as either Kazoo JSON or TwiML, which will be rendered. Pivot never continues 
execution of the current document after an `action`, further terms will be unreachable.  

### `callerId`
the caller ID the callee will see. Can be set to any value. Defaults to the caller's caller ID. 

### `hangupOnStar`
Allows the original caller to hang up on the dialed party without ending the Pivot session by 
pressing the * key. Dial execution completes, including sending requests to the `action` url if 
provided, and Pivot will attempt to execute the next term in the doucument.  

### `method`
sets the http request method for the (`action`)[#action] request, either `POST` or `GET`. Pivot 
expects a response with more call instructions. See [Requests](./../requests.md) for information 
about the request and respose formats.  
Defaults to `GET`.  

### `timeLimit`
Maximum call duration, in seconds. After `timeLimit` seconds pivot will automatically hang up the 
call, defaults to 14400 seconds (4 hours).  

### `timeout`
Pivot will wait for the called party to answer for `timeout` seconds. 

\*Pivot always adds an additional five seconds to `timeout`. for example `timeout` may be 
configured to 0 seconds, but because of this additional buffer, the true minimum time Pivot will 
wait for an answer is 5 seconds. The default value for the `timeout` parameter is 30 seconds, so 
the true default time Pivot waits on an answer by default is 35 seconds.

## Nesting Nouns

Pivot supports several TwiML nesting nouns for <Dial>, allowing callers to be connected to a 
variety of endpoints.  

| Noun                                   | Description                                                                              |
|----------------------------------------|------------------------------------------------------------------------------------------|
| plain text                             | simple dial. Pivot expects a complete phone number which can be offnet                   |
| [`<Conference>`](./dial/conference.md) | Conference room endpoint                                                                 |
| [`<Device>`](./dial/device.md)         | ID of an existing Kazoo Device                                                           |
| [`<Number>`](./dial/number.md)         | Plain text <Dial> with extended attributes                                               |
| [`<Sip>`](./dial/sip.md)               | SIP URI to dial                                                                          |
| [`<User>`](./dial/user.md)             | ID of an existing Kazoo User, see (Callflow Users)[]                                     |

