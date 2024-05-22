# Requests and Responses

Pivot sends some general information along side the call's parameters to your server when making
a request, and can encode these parameters differently to match your API.

## Pivot Options

The first request to your server will always come by way of 
[Callflow](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/README.md). Even when using the
Pivot GUI app, behind the scenes {% BRAND_NAME %} uses the `pivot` Callflow module to route a call 
to Pivot for you.

The `pivot` Callflow JSON module has options which modify how Pivot sends requests to your server.
When using the Pivot GUI app, only the `voice_url`, `method` and `req_format` can be used.
the others are available through Advanced Callflow and the Callflow API.

Call data can be sent as a URL encoded query string or in the body of the request 
depending on the values of `req_body_format` and `method`.
- When `req_body_format` is set to form, parameters are url encoded.
- When `req_body_format` is set to `json` and `method` to `post`, parameters are sent in the request body in JSON format.

In this sample, Pivot would send parameters as a JSON document in the the request body:
```json
{
  "module": "pivot",
    "data": {
      "voice_url": "http://your.pivot.server/",
      "req_body_format": "json",
      "method": "post",
    }
}
```

| Key                      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `voice_url`              | What URL to send the request when a call comes into this Pivot callflow. **This field is required.**<br><br> The URL is stricly checked to be a valid HTTP or HTTPS URL and hostname if used must be resolvable through DNS. The URL must contain schema and hostname or IP and an optional port or user name password for basic authentication.<br><br> Please be note depends on system configuration `127.0.0.1/32`, `0.0.0.0/32` IPs and hostname `localhost` may by default blocked. System administrator may also blocked RFC1918 IPs `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`.<br><br>Valid hostname looks like:<br><br>* `https?://[user@pass]client.host[:port]`<br><br>Required parameter, no default. |
| `method`                 | What HTTP method to send the request(s) with. Possible values are:<br><br>* `get`<br>* `post`<br><br> Defaults to `get`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `req_format`             | Request parameter format Pivot will use. Valid options are:<br>* `kazoo`<br>* `twiml`<br><br> See [Request Parameters](#request-parameters) section for more info. <br><br>Defaults to `kazoo`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `req_body_format`        | Format to encode the request body, including request parameters when using `POST`, valid options are<br><br>* `form`: request body will be encoded in `application/x-www-form-urlencoded` format as a query string<br>* `json`: request body will be formatted in `application/json` <br><br>Defaults to `form`                                                                                                                                                                                                                                                                                                                                                                                                           |
| `cdr_url`                | URL to send the [CDR](https://docs.2600hz.com/git/2600hz/kazoo-cdr/doc/README.md) to at the end of the call. If used, CDRs for for the initial call and any additional calls connected during the Pivot session are sent to this URL at the end of the call.<br>The body is encoded in `application/x-www-form-urlencoded` format.<br>Note that {% BRAND_NAME %} always stores CDRs accessible through the [CDRs API](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/cdrs.md). This parameter allows for the CDRs to additionally be sent to developer servers.<br><br>No Default                                                                                                                                                                                                                                           |
| `debug`                  | Store debug logs of the requests and responses to your application. These logs are then available via the [Pivot API](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/pivot.md)<br><br>Defaults to `false`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `req_timeout_ms`         | How long, in milliseconds, to wait for a Pivot response from the developer server. maximum value is 5 seconds. {% BRAND_NAME %}  highly recommends that your application respond to requests under 5 seconds to avoid call quality issues.<br><br>Defaults to `5000`                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `custom_request_headers` | Custom HTTP headers to include on each Pivot request to the `voice_url`. {% BRAND_NAME %} expects these headers to conform to the HTTP protocol spec. For example, header names must be prefixed with `X-`<br>Used to pass additional data to developer servers.<br><br>No Default                                                                                                                                                                                                                                                                                                                                                                                                                                        |

## Request Parameters

Pivot can send parameters with Pivot TwiML style keys, or with Callflow style keys, depending on
the value of `req_format`.
- when set to `twiml`, Pivot will use Pivot TwiML parameters.
- when set to `kazoo`, Pivot will use Callflow Parameters.

These parameters are sent in every request:

| Callflow Parameters | Pivot TwiML Parameters | Description                                                                                                                      |
|---------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| `Account-ID`        | `AccountSid`           | The id of the {% BRAND_NAME %} accout receiving the call                                                                         |
| `Api-Version`       | `ApiVersion`           | The version of the API                                                                                                           |
| `Call-ID`           | `CallSid`              | The unique call identifier                                                                                                       |
| `Caller-ID-Name`    | `CallerName`           | Caller ID Name                                                                                                                   |
| `Caller-ID-Number`  | `CallerNumber`         | Caller ID Number                                                                                                                 |
| `Direction`         | `Direction`            | The direction of the call, relative to {% BRAND_NAME %}: `outbound` if {% BRAND_NAME %} originated the call, `inbound` otherwise |
| `From-Realm`        | `FromRealm`            | From SIP realm                                                                                                                   |
| `From`              | `From`                 | Caller's number, if available                                                                                                    |
| `Language`          | -                      | The caller's language preference                                                                                                 |
| `To-Realm`          | `ToRealm`              | To SIP realm                                                                                                                     |
| `To`                | `To`                   | Dialed number, SIP endpoint or user                                                                                              |
| `User-ID`           | -                      | The User ID(s) of the caller                                                                                                     |

So if your application needs to route some calls differently depending on the phone number, your
Server can use `Caller-ID-Number` field of the request to handle that.

### Optional Parameters

The request may include these parameters after certain callflow modules or Pivot TwiML terms are executed.

| Callflow Parameters       | Pivot TwiML Parameters | Description                                             |
|---------------------------|------------------------|---------------------------------------------------------|
| `Call-Status`             | `CallStatus`           | Status of the call                                      |
| `Custom-Application-Vars` | -                      | Custom application variables set on the call channel    |
| `Custom-SIP-Headers`      | -                      | Custom SIP Headers set on the call channel              |
| `DialCallDuration`        | `DialCallDuration`     | How many billable seconds in the call                   |
| `DialCallSid`             | `DialCallSid`          | Call-ID of the b-leg                                    |
| `DialCallStatus`          | `DialCallStatus`       | Call status of the b-leg                                |
| `Digits`                  | `Digits`               | Any DTMF (or collection of DTMFs) pressed               |
| `Recording-Duration`      | `RecordingDuration`    | How long the recording is                               |
| `Recording-ID`            | `RecordingSid`         | The recording id                                        |
| `Recording-Url`           | `RecordingUrl`         | The URL that can be used to retrieve the recorded media |
| `Request-Realm`           | -                      |                                                         |
| `Request`                 | -                      |                                                         |
| `User-ID`                 | -                      |                                                         |

## Responses

Pivot expects a `200` response which includes a `Content-Type` header, which tells pivot if your
resonse contains Pivot TwiML or Callflow JSON.
- when the value is `text/xml` or `application/xml`, Pivot expects Pivot TwiML
- when the value is `application/json`, pivot expects Callflow JSON
- Any usupported `Content-Type` will result in a failure.

If your server decides not to handle a call, it is still responsible for sending a 
response to end the call. For a quick no answer, the server can send `200 OK`, with
`Content-Type: aplication/xml` and body `<Response/>`.

If a request times out or otherwise fails Pivot will end the call immediately, 
unless the first request of a call times out.
When that happens, {% BRAND_NAME %} will fallback on the callflow that initiated the Pivot.
When routing to Pivot using the Pivot GUI app, the remainder of the callflow is always empty, so
the call will end.
This only applies to the initial Pivot request for each call. A request failing after the
first results in call termination.

Your server's responses can and often will include instructions for Pivot to make further requests.
This is done using either the `pivot` module in a Callflow JSON response, or the `action`
parameter of many Pivot TwiML terms.

Your server can always respond with valid Callflow JSON or Pivot TwiML--it does not matter in what 
format Pivot sent the parameters.

It also does not matter what your server has previously sent to Pivot, even for a single call.
For example, if your first response contained Pivot TwiML instructions, After
another request from Pivot for the same call, your server may respond with Callflow JSON and
Pivot will execute it.

For example, if Pivot makes a request to `your.server.com/pivot_twiml` and your server responds:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>This is a Pivot TwiML response. Try Callflow JSON next</Say>
    <Redirect>your.server.com/callflow_json</Redirect>
</Response>
```
Pivot will make another request after rendering [`<Redirect>`](./twiml/redirect.md), and your server
could respond with:

```json
{
  "module": "tts",
  "data": {
    "test": "Wow, this is a Callflow JSON response! Lets try Pivot TwiML next"
  },
  "children": {
    "_": {
      "module": "pivot",
      "data": {
        "voice_url": "your.server.com/pivot_twiml"
      }
    }
  }
}
```

Pivot will happily process these forever until the caller hangs up.
Take care to avoid infinite loops like this building your own Pivot systems.