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

### Pivot Callflow

@TODO add intro? description?

| Key                      |  Description |
| -------------------------| -------------|
| `voice_url`              | What URL to send the request when a call comes into this Pivot callflow. **This field is required.**<br><br> The URL is stricly check to be a valid HTTP or HTTPS URL and hostname if used must be resolvable through DNS. The URL must contain schema and hostname or IP and an optional port or user name password for basic authentication.<br><br> Please be note depends on system configuration `127.0.0.1/32`, `0.0.0.0/32` IPs and hostname `localhost` may by default blocked. System adminstrator may also blocked RFC1918 IPs `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`.<br><br>Valid hostname looks like:<br><br>* `https?://[user@pass]client.host[:port]` |
| `method`                 | What HTTP method to send the request(s) with. Possible values are:<br><br>* `get`<br>* `post`<br><br> Default value is `get`. |
| `req_format`             | What type of parameter Pivot must use when sending the request. Valid options are: <br><br>* `kazoo`<br>* `twiml`<br><br> See Request Parameters section for more info. Default value is `kazoo`. |
| `req_body_format`        | What format should the request body have encoded in when using `POST` method. Valid options are:<br><br>* `form`: request body will be encoded in `application/x-www-form-urlencoded` format<br>* `json`: request budoy will be format in `application/json` <br><br> Default value is `form`. |
| `debug`                  | Store debug logs of the requests and responses to your application. Default to `false`. These logs are then available via the [Pivot API](/kazoo/5x-stable-api-ref/call-control/advanced-features/pivot/pivot#debugging-pivot-attempts) |
| `cdr_url`                | Optional URL to send the CDR to at the end of the call, useful to store and/or inspect Call Detail Record (CDR) after the call is ended. If used, CDRs for inital call and any outbound call started by `Dial` simple dial or `Numbers` noun ends a HTTP `POST` request will be sent to this URL. The body is encoded in `application/x-www-form-urlencoded` format.<br><br> By default no request to store CDRs is made. Please be advise Kazoo alwats store CDRs of each leg of the call, which can be retrived using CDRs API. |
| `custom_request_headers` | Custom HTTP headers to be included on each Pivot request to the `voice_url`. Header names per HTTP protocol spec are prefix by `X-`. Header values can be anything that is allows as HTTP header value. You may useful this to pass additional data in HTTP header to your application. |
| `req_timeout_ms`         | How long, in milliseconds, to wait for a Pivot response from the HTTP server. Default and maximum value is 5 seconds. We highly recommend that your application to respond to request uner 5 seconds to avoid call quality issues. |

### Request Parameters

Pivot will include some data related to the received call in the request to your application to provide the data you can use to create the response.
We advise your application t responde quicky as much as possible to avoid so much silence for the caller.

What paramters are being sent and how they are encoded depends on your Pivot callflow configuration. By default Pivot sends Kazoo type parameters and will encode it as `application/x-www-form-urlencoded`.

Depends on your application, you may choose the type and format of parameters you want to get. Kazoo supports to types:

* [Kazoo Request Parameters](./kazoo/request_params.md): The standard parameters of calls used across the Kazoo.
* [TwiML Request Parameters](./twiml/request_params.md): A variation of Kazoo calls data as subset of TwiML to help you onboard easy to Kazoo platform before fully integerating to Kazoo.

### Create Pivot Callflow

As said above the first step to use Progammable Callflow is to define a regular Callflow with `pivot` as module. You can use
the usual way of creating Callflow, or you can use Pivot Monsterf UI or Commland apps to help you set up.

@TODO:
- maybe explain or show and example to create using API
- show how to create using commland app or monster ui with screenshot?
- do not over explain parameters here, refer to Pivot request to your application parameters section
- if this section gets to big move it to a separate page so we can link it.


## Your Application respond to Pivot

After Pivot made the request to `voice_url`, your application can instruct how Kazoo to handle the received call.
Maybe you want to recrod the call, or use TTS to say a text to the caller or play an audio
file or any other actions you may want.

You application must respond in maximum of 5 seconds before the Pivot coniders the reuest is failed.

Pivot expect `200 OK` HTTP code with `Content-Type` header to indicate the translator to
use for processing the response. If no `Content-Type` is set, `text/xml` will be assumed.
Any not supported `Content-Type` will result in failure and Pivot hangup the call.

Your application may choose to respond a Pivot any supported response translator that Pivot supports.
Each response can use different translator independably.

Please be advise if your application does not need to handle the call, you are still
responsible to end the call with either translator. For quick no answer, your application
can send `200 OK`, with `Content-Type: aplication/xml` and body `<Response />`.

If HTTP request to your application fails in intial request or any other subsquent requests ails, Pivot will stop and hangup the caller.

### Pivot Translator

@TODO description

* [Kazoo JSON](./kazoo/README.md): This is same Callflow JSON that you already familiar with and happey with. Using this translator you have the
full power of Callflow at your hand and behave exactly like a normal Callflow will act. This gives you more power to control and dynamically create callflows per call basis.
* [TwiML](./twiml/README.md): THis taylored more to epople that want easy migration to Kazoo from other platform. This is option gives a more restricted way to handle the call compare to Callflow.
