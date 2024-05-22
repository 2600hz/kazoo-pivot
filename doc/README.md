# Pivot - Programmable flows

Pivot is a powerful and flexible way to build flows. Pivot flows are a string
of Callflow modules or Pivot TwiML terms which describe how to handle a call.
Pivot is especially well suited to building dynamic call systems where the actions on a call are
dependent on systems or information in your application. 
Pivot also integrates easily with existing call systems because of it can interpret [Pivot TwiML](./twiml/README.md).

Pivot can render this:
```json
{
  "module": "tts",
  "data": {
    "text": "You're using Callflow JSON!"
  }
}
```
And this!
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>You're using Pivot TwiML!</Say>
</Response>
```


## Routing and Call Control in {% BRAND_NAME %}

In addition to Pivot, {% BRAND_NAME %} offers 
[Smart PBX](https://docs.2600hz.com/frontends/monsterui/user-guides/app-docs/smartpbx/user-guide/)
for basic routing and device management, and [Callflow](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/README.md) 
for call control and automation usable via API or a 
[click-and-drag](https://docs.2600hz.com/developers/pivot/pivot-programmable-flows/) flow builder. 
Pivot can use all the modules available through Callflow, and allows you
to define and execute flows dynamically.

Please note that flows created using Callflow are called "callflow", as apposed to the language
Pivot and Callflow use to define flows, "Callflow JSON". 

## How Pivot Works

When Pivot receives a call, it makes a request to your server with [information](../request_params.md#request-parameters)
about the call. Your server can respond with either [Pivot TwiML](../twiml/README.md) or [Callflow JSON](./README.md) instructions,
which Pivot will then execute on the call. You may instruct pivot to make additional requests for more
instructions during a call, and can freely switch between replying with Pivot TwiML and Callflow JSON between requests.

For example, the caller will hear "Hello Pivot" If your application replies with this Callflow JSON:

```json
{
  "module": "tts",
  "data": {
    "text": "Hello Pivot!"
  }
}
```

You have control over how Pivot sends request to your applications and how your application responds.

## Routing Calls to Pivot

Calls are routed using the Pivot GUI app, through the Advanced Callflow UI or through the Callflow HTTP API.

Any callflow can use the `pivot` module to direct a call to Pivot. This example is a flow created using 
Advanced Callflow or the Callflow HTTP API instead of the Pivot GUI app:

```json
{
  "numbers": ["2030"],
  "flow": {
    "module": "pivot",
    "data": {
      "voice_url": "http://your.pivot.server/path/to/script.php",
      "req_format": "kazoo",
      "method": "get",
      "debug": true
    }
  }
}
```

When someone calls the extension `2030`, this flow will execute and ask Pivot to take control of call.
Pivot then sends a `GET` HTTP request to `http://your.pivot.server/path/to/script.php`.

Your application may respond with either a JSON or XML payload. For example, this server responds with Callflow JSON to play TTS:

```json
{
    "module": "tts",
    "data": {
        "text": "Pivot is pretty awesome. Have a great day."
    },
    "children": {}
}
```

{% BRAND_NAME %} will then execute this JSON, and the caller will hear "Pivot is pretty awesome. Have a great day.", {% BRAND_NAME %} will attempt to execute any
child modules as well, but because there are no additional modules in this flow the call will end.

See [Switching From Twilio&trade;](../twiml/switchover.md#migrating-from-twilio-sdk) for an additional example using
Pivot TwiML and Callflow JSON together, and [Requests and Responses](../request_params.md) for more information
on how your server and Pivot will communicate.

## Examples and Useful Modules

### Text To Speech

The [`tts`](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/tts.md) Callflow module
and the Pivot TwiML [`<Say>`](./twiml/say.md) term are used to speak to callers.

```json
{
  "module": "tts",
  "data": {
    "text": "Pivot is pretty awesome. Have a great day."
  }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>Pivot is pretty awesome. Have a great day.</Say>
</Response>
```

### Bridging a call to another party

[Several](./kazoo/bridging.md) Callflow modules bridge callers to new parties depending on the type of
party to connect, similar to the different nested terms in [`<Dial>`](./twiml/dial.md).

```json
{
  "module": "resources",
  "data": {
    "to_did": "{Another Number}",
    "use_local_resources": false
  }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>{Another Number}</Dial>
</Response>
```

See the respective pages for more examples.

### Recording a call

Many Callflow JSON modules and Pivot TwiML terms work similarly, but call recording
is handled differently.

The [`record_call`](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/record_call.md)
Callflow module begins a recording and then continues executing
the children of the module.

```json
{
  "module": "record_call",
  "data": {
    "action": "start",
    "record_on_answer": true
  },
  "children": {
    "_": {
      "module": "resources",
      "data": {
        "to_did": "{Your phone number}",
        "use_local_resources": false
      },
    }
  }
}
```

The [`<Record>`](./twiml/record.md) Pivot TwiML term halts execution during recording,
so other terms will not execute until recording is complete. To record during execution,
use [the record property of dial`<Dial>`](./twiml/dial.md#record).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>{Your phone number}</Dial>
    <Say>Recording for 10s</Say>
    <Record timeout="10"></Record>
    <Say>Thank You</Say>
</Response>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial record="record-from-answer">{Your phone number}</Dial>
    <Say>Already Recording</Say>
</Response>
```

### IVR functionality

A Pivot IVR requires using multiple Callflow Modules and Pivot TwiML
terms, likely over [multiple requests](./request_params.md) to your server. The 
[`collect_dtmf`](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/collect.md) module is designed 
for caller input in Pivot's dynamic environment. 

```json
{
  "module": "collect_dtmf",
  "data": {
    "max_digits": 4,
    "collection_name": "my_dtmf"
  },
  "children": {
    "_": {
      "module": "pivot",
      "data": {
        "voice_url": "http://pivot.your.company.com/collected.php"
      }
    }
  }
}
```
The DTMF will be available in the child [request](./request_params.md) to your server.


{% BRAND_NAME %} also provides the 
[`menu`](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/menu.md) module as an all-in-one
IVR solution hosted on our platform. Menus are configured by API and offer a host of self-managing
features, including updates by phone.

The Pivot TwiML [`<Gather>`](./twiml/gather.md) works similarly to the `collect` module:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Gather action="http://pivot.your.company.com/gathered.php"></Gather>
</Response>
```

## Additional Examples

### Time Of Day Routing

For an example of Pivot and Callflow powerful routing capabilities, see
[Time of Day Routing](examples/time_of_day.md)
