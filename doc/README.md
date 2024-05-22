# Pivot - Programmable Callflow

## Overview

The Pivot gives developers greater control over callflows than what comes natively in Kazoo. Pivot attempts to corral the salient data and send it, via HTTP to the developer's web server.
Pivot expects a response with appropriate TwiML XML or Kazoo Callflow JSON, and will execute the callflow returned on behalf of the developer.

Pivot works with an existing call and cannot iniate a call by itself.

## How Pivot works

You start by creating a special Callfow in your account with `pivot` module. When some one calls the number assigned to that Callflow, callflow uses
the configured URL of your application in the flow and ask Pivot application to take control of the call. Pivot application then sends a request to the URL
to get the instruction of what to with the call.

Your application can now uses the request paramters and Kazoo HTTP API to control and interact with the call. Like for example play a media, record the call or make an offnet call.

You have control over how Pivot sends request to your applications and what your application can respond. @TODO: links?

### Example Pivot workflow

Starting with creating a progammable callflow, use Advanced Callflow UI app or Callflow HTTP API:

```json
{
 "numbers": ["2030"],
 "flow":{
     "module": "pivot",
     "data":{
         "voice_url": "http://your.pivot.server/path/to/script.php",
         "req_format": "kazoo",
         "method": "get",
         "debug": true
     }
 }
}
```

When someone calls `2030` extension progammable callflow will execute and asks Pivot application to take control of call.
Pivot application sends a `GET` HTTP request to `http://your.pivot.server/path/to/script.php`.

Your application may respond in JSON or XML payload, for example here you application respond with a JSON to play a TTS text:

```json
{
    "module": "tts",
    "data": {
        "text": "Pivot is pretty awesome. Have a great day."
    },
    "children": {}
}
```

## Pivot make request to your application

You can create and control how to route an incoming call to your application for handling. You create a callflow and configue some paramters of request.

When the Pivot callflow is triggered, Callflow sends the request to Pivot application. Pivot application take control of the call and tries to
send request to your external application over HTTP for handling the call. Since this is a normal callflow, you may assign multiple DIDs or extensions or
patterns to the callflow.

@TODO should we say calllfow may continue processing the original callflow if the http reqeust fails?
@TODO we may want to advise something here but I forgot what

Bare minimum required configuration is the URL of your application. Other parameters are optional. By default Pivot application will make
a HTTP `GET` to the specified URL with all paramters passed as encoded string query.

You application must respond in maximum of 5 seconds before the Pivot coniders the reuest is failed.

You may optionally request to log debug infomration about the request Pivot is sending and the response it is getting and if the outcome of the call if there is any.
You may use this log to inspect and debug your request and responses and know the possible reason why a certain call failed.

### Pivot Callflow configuration

@TODO:
- explain these request confiuration
- preferable a table with name of configuration desription
    - if this gets so big maybe table first with short summary and links to sections for each config?
- fields:
  - voice_url
    - make 
  - method
  - req_body_format
  - req_format
  - debug
  - cdr_url
  - custom_request_headers
  - req_timeout_ms
- schema for reference applications/crossbar/priv/couchdb/schemas/callflows.pivot.json

| Config Key               |  Description                                                                                    |
| -----------------        | ----------------------------------------------------------------------------------------------- |
| `voice_url`              | What URL to send the request when a call comes into this Pivot callflow. **This field is required.**<br><br> The URL is stricly check to be a valid HTTP or HTTPS URL and hostname if used must be resolvable through DNS. The URL must contain schema and hostname or IP and an optional port or user name password for basic authentication.<br><br> Please be advised that The RFC1918 IPs (`10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`) `127.0.0.1/32` and `0.0.0.0/32` IPs and hostname `localhost` are by default blocked.<br><br>Valid hostname looks like:<br>`https?://[user@pass]client.host[:port]` |
| `method`                 | What HTTP method to send the request(s) with. Possible values are:<br>- `get`<br>- `post`<br><br> Default value is `get`. |
| `req_format`             | What type of parameter Pivot must use when sending the request. Valid options are: <br> - `kazoo` <br> - `twiml` <br><br> See Response format section for more info. Default vvalues is `kazoo`. |
| `req_body_format`        | What format should the request body have when using `POST` method. Valid options are: <br> - `form`: request body will be encoded in `application/x-www-form-urlencoded` format <br> - `json`: request budoy will be format in `application/json` <br><br> Default value is `form`. |
| `debug`                  | Store debug logs of the requests and responses to your application. Default to `false`. These logs are then available via the [Pivot API](/kazoo/5x-stable-api-ref/call-control/advanced-features/pivot/pivot#debugging-pivot-attempts) |
| `cdr_url`                | Optional URL to send the CDR to at the end of the call, useful to store and/or inspect Call Detail Record (CDR) after the call is ended. If used, CDRs for inital call and any outbound call started by `Dial` simple dial or `Numbers` noun ends a HTTP `POST` request will be sent to this URL. The body is encoded in `application/x-www-form-urlencoded` format.<br><br> By default no request to store CDRs is made. Please be advise Kazoo alwats store CDRs of each leg of the call, which can be retrived using CDRs API. |
| `custom_request_headers` | Custom HTTP headers to be included on each Pivot request to the `voice_url`. Header names per HTTP protocol spec are prefix by `X-`. Header values can be anything that is allows as HTTP header value. You may useful this to pass additional data in HTTP header to your application. |
| `req_timeout_ms`         | How long, in milliseconds, to wait for a Pivot response from the HTTP server. Default and maximum value is 5 seconds. We highly recommend that your application to respond to request uner 5 seconds to avoid call quality issues. |

### Request Paramters

Pivot will include some data in the request to your application to provide the data you can use to create the response.
We advise your application t responde quicky as much as possible to avoid so much silence for the caller.

What paramters are being sent and how they are encoded depends on your Pivot callflow configuration. By default Pivot sends Kazoo type parameters and will encode it as `application/x-www-form-urlencoded`.

#### Kazoo Request Parameters

The request may include any of these parameters if they exists on the call.

@TODO add descriptions

| Parameter                 | Description |
|---------------------------|-------------|
| `Account-ID`              |             |
| `Api-Version`             |             |
| `Call-ID`                 |             |
| `Call-Status`             |             |
| `Caller-ID-Name`          |             |
| `Caller-ID-Number`        |             |
| `Custom-Application-Vars` |             |
| `Custom-SIP-Headers`      |             |
| `Digits`                  |             |
| `Direction`               |             |
| `From-Realm`              |             |
| `From`                    |             |
| `Language`                |             |
| `Recording-Duration`      |             |
| `Recording-ID`            |             |
| `Recording-Url`           |             |
| `Request-Realm`           |             |
| `Request`                 |             |
| `To`                      |             |

@TODO call-status

#### TwiML Request Paramters

The request may include any of these parameters if they exists on the call.

@TODO add descriptions

| Parameter             | Description |
|-----------------------|-------------|
| `AccountSid`          |             |
| `ApiVersion`          |             |
| `CallSid`             |             |
| `CallStatus`          |             |
| `CallerName`          |             |
| `CallerNumber`        |             |
| `Digits`              |             |
| `Direction`           |             |
| `FromRealm`           |             |
| `From`                |             |
| `RecordingDuration`   |             |
| `RecordingSid`        |             |
| `RecordingUrl`        |             |
| `ToRealm`             |             |
| `To`                  |             |
| `TranscriptionSid`    |             |
| `TranscriptionStatus` |             |
| `TranscriptionText`   |             |
| `TranscriptionUrl`    |             |
| `TranscriptionUrl`    |             |

@TODO call-status

### Create Pivot Programmable Callflow

As said above the first step to use Progammable Callflow is to define a regular Callflow with `pivot` as module. You can use
the usual way of creating Callflow, or you can use Pivot Monsterf UI or Commland apps to help you set up.

@TODO:
- maybe explain or show and example to create using API
- show how to create using commland app or monster ui with screenshot?
- do not over explain parameters here, refer to Pivot request to your application parameters section
- if this section gets to big move it to a separate page so we can link it.


## Response Formats

Your application response will use as instruction to be excute on the call. You have to options for the format of your application response.

* [Kazoo JSON](./kazoo/README.md): This is same Callflow JSON that you already familiar with and happey with. Using this format you have the
full power of Callflow at your hand and behave exactly like a normal Callflow will act. This gives you more power to control and dynamically create callflows per call basis.
* [TwiML](./twiml/README.md): THis taylored more to epople that want easy migration to Kazoo from other platform. This is option gives a more restricted way to handle the call compare to Callflow.
