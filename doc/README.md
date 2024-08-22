# Pivot - Programmable Callflow

Pivot gives developers greater control over Callflows than what comes natively in Kazoo. Pivot attempts to gather relevant call data and then sends it, via HTTP to the developer's web server.
Pivot expects a response with appropriate TwiML XML or Kazoo Callflow JSON, and will execute the callflow returned. The developer server can generate and reply with any callflow actions, so anything that can be done with callflows can be done dynamically with Pivot.

Pivot works with an existing call and cannot initiate a call by itself.


## How Pivot works

A Pivot action can be created in your account using the Pivot app or using Advanced Callflows. When the assigned number is called, Pivot will make a request to the configured URL. The developer server can use the `request parameters` pivot sends to control and interact with the call. 

The Pivot app creates a special Callflow with one module, `pivot`. When a call comes in, Callflows gives control of the call to Pivot, which makes an HTTP request to the configured server, expecting a reply containing Callflow JSON or supported TwiML XML.

You have control over how Pivot sends request to your applications and what your application can respond.

### Example Pivot workflow

Any programmable callflow can use the `pivot` module. This example will use the Advanced Callfow UI or the Callflow HTTP API instead of the Pivot App to create a pivot:
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

When someone calls the extension `2030`, this programmable callflow will execute and ask Kazoo Pivot, the backend Pivot application to take control of call.
Kazoo Pivot then sends a `GET` HTTP request to `http://your.pivot.server/path/to/script.php`.

Your application may respond with a JSON or XML payload, for example, this server  responds with Callflow JSON to play TTS:

```json
{
    "module": "tts",
    "data": {
        "text": "Pivot is pretty awesome. Have a great day."
    },
    "children": {}
}
```
Kazoo will then render this JSON, and the caller will hear "Pivot is pretty awesome. Have a great day." Kazoo will attempt to continue rendering the returned JSON. Because there are no additional `modules` in this callflow, the call will end.

### Pivot Module Parameters

Required and optional parameters for the callflow `pivot` module. The Pivot App creates an action using only `voice_url`, `method` and `req_format`

| Key                      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `voice_url`              | What URL to send the request when a call comes into this Pivot callflow. **This field is required.**<br><br> The URL is stricly check to be a valid HTTP or HTTPS URL and hostname if used must be resolvable through DNS. The URL must contain schema and hostname or IP and an optional port or user name password for basic authentication.<br><br> Please be note depends on system configuration `127.0.0.1/32`, `0.0.0.0/32` IPs and hostname `localhost` may by default blocked. System adminstrator may also blocked RFC1918 IPs `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`.<br><br>Valid hostname looks like:<br><br>* `https?://[user@pass]client.host[:port]`<br><br>Required parameter, no default. |
| `method`                 | What HTTP method to send the request(s) with. Possible values are:<br><br>* `get`<br>* `post`<br><br> Defaults to `get`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `req_format`             | Request parameter format Pivot will use. Valid options are:<br>* `kazoo`<br>* `twiml`<br><br> See [Request Parameters](./request_params.md) section for more info. <br><br>Defaults to `kazoo`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `req_body_format`        | Format to encode the request body, including request parameters when using `POST`, valid options are<br><br>* `form`: request body will be encoded in `application/x-www-form-urlencoded` format as a query string<br>* `json`: request bdoy will be formatted in `application/json` <br><br>Defaults to `form`                                                                                                                                                                                                                                                                                                                                                                                                          |
| `debug`                  | Store debug logs of the requests and responses to your application. These logs are then available via the [Pivot API](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/pivot.md)<br><br>Defaults to `false`                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `cdr_url`                | URL to send the CDR (Call Data Record) to at the end of the call. If used, CDRs for for the initial all and any additional calls connected during the pivot session will be sent to this URL at the end of the call.<br>The body is encoded in `application/x-www-form-urlencoded` format.<br>Note that Kazoo always stores CDRs accessible through the CDRs API. This parameter allows for the CDRs to additionally be sent to developer servers.<br><br>No Default                                                                                                                                                                                                                                                     |
| `custom_request_headers` | Custom HTTP headers to include on each Pivot request to the `voice_url`. Kazoo expects these headers to conform to the HTTP protocol spec. For example, header names must be prefixed with `X-`<br>Used to pass additional data to developer servers.<br><br>No Default                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `req_timeout_ms`         | How long, in milliseconds, to wait for a Pivot response from the developer server. maximum value is 5 seconds. 2600Hz  highly recommends that your application respond to requests under 5 seconds to avoid call quality issues.<br><br>Defaults to `5000`                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
## Developer Server Response

Pivot expects a `200` response which includes a `Content-Type` header. Pivot uses the value of `Content-Type` to determine how to parse the response.
If no `Content-Type` is set, `text/xml` will be assumed. Any unsupported `Content-Type` will result in failure. `Content-Type` can change with each request/response within one Pivot Session.

Please note that if the developer server decides not to handle a call, it is still responsible for sending a response to end the call. For quick no answer, the server can send `200 OK`, with `Content-Type: aplication/xml` and body `<Response/>`.

If a request times out or otherwise fails, Pivot will end the call with [one exception](#edge-cases).
### Supported Callflow Formats
Currently Pivot supports Kazoo JSON and TwiML XML responses from developer servers.

#### Kazoo JSON
[Kazoo JSON](./kazoo/README.md) is the Callflow JSON used throughout Kazoo. With this format the full power of Callflows is available, and actions behave exactly as they do in a normal callfow. Pivot uses this format when the `Content-Type` header of the developer server's response is `application/json`.

For more complete reference of available actions, see the [Callfow Actions Reference Manual](https://docs.2600hz.com/kazoo/callflow/).

#### TwiML XML

Pivot supports a subset of TwiML Verbs and Nouns for easier interoperability with and migration from Twilio. Some advanced Callflow actions may not be replicable using only TwiML support. Pivot uses this format when the `Content-Type` header of the developer server's response is `application/xml` or `text/xml`.

## Edge Cases

Edge cases developers and integrators may wish to be aware of.

#### The first HTTP request to the developer server fails

A `pivot` is almost always a terminal callflow action. However, if the initial pivot request fails, Kazoo will fallback and continue executing the programmable Callflow that created the pivot. 
For pivot callflows created using the Pivot App, the callflow is otherwise empty, so Kazoo ends the call.
#### Subsequent requests to the developer server fail

Pivot can make more than one request to developer servers when pivot callflows contain TwIML `action` terms or additional `pivot` modules. If those fail, Pivot ends the call.
