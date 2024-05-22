# \<Record>  

Record and store calls  

## Overview  

Pivot will begin recording calls when this term is rendered. Pivot will not render additional 
terms until `<Record>` completes.

{% BRAND_NAME %} will only store call recordings if the account has an appropriate 
[Storage Plan](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/storage.md) setup. 
For accounts without storage for recordings, Pivot can send recordings to a developer server 
using the `recordingUrl` attribute. To retrieve calls stored by {% BRAND_NAME %}, see 
[Call Recordings API](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/recordings.md).

For the [Callflow Json](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/README.md) equivalent, see 
[`record call`](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/record_call.md). 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>Recording for 10 seconds</Say>
    <Record timeout="10"></Record>
    <Say>Thank you</Say>
</Response>
```
The caller will hear silence for 10 seconds during the recording period before hearing
"Thank you".


## Attributes  

| Name                                 | Values Description                            | Default value  |
|--------------------------------------|-----------------------------------------------|----------------|
| [`finishOnKey`](#finishOnKey)        | Any DTMF character                            | `0123456789*#` |
| [`method`](#method)                  | `GET` \| `POST`                               | `GET`          |
| [`playBeep`](#playBeep)              | `true` \|`false`                              | `true`         |
| [`maxLength`](#maxLength)            | any positive integer (seconds)                | 3600 (1 hour)  |
| [`timeout`](#timeout)                | any positive integer including zero (seconds) | 5              |
| [`action`](#action)                  | URL                                           | none           |
| [`recordingUrl`](#recordingUrl)      | URL                                           | none           |
| [`recordStoreMethod`](#recordingUrl) | `PUT` \| `POST` \| `PATCH`                    | `PUT`          |

### `action`  

The URL where Pivot will make an HTTP request once the `<Record>` term completes.
[`method`](#method) sets the HTTP method of the request. Pivot expects another Pivot document in 
response as either {% BRAND_NAME %} JSON or TwiML, which will be rendered. Pivot never continues execution 
of the current document after an `action`, further terms will be unreachable.  

### `finishOnKey`

When pressed, this key finishes recording. `<Record>` will then complete. By default, any DTMF 
character will end recording.  

### `maxLength`

Pivot will stop recording after `maxLength` seconds and complete rendering `<Record>`.  

### `method`

Pivot sets the HTTP request method for the [`action`](#action) request, either `POST` or `GET`. 
Pivot expects a response with more call instructions. See [Requests](../request_params.md) for 
information about the request and response formats. 

Defaults to `GET`.  

### `playBeep`

If set to true, Pivot will play a beep as recording starts. 

Defaults to `true`.

### `timeout`

Pivot will record silence on the line for `timeout` seconds before ending the recording. 
`<Record>` can still continue after a recording timeout. 

`timeout="0"` will disable timeout.

### `recordingUrl`

{% BRAND_NAME %} will attempt to send recordings to this URL plus a generated media name when set. For example, when 
`recordingUrl=myrecordings.com/pivot`, {% BRAND_NAME %} will send a recording to `myrecordings.com/pivot/\[mediaName]`

### `recordStoreMethod`

Method to use when sending media to `recordingUrl`.
