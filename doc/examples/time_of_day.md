# Example: Time of Day Routing

This is a simple example to demonstrate how your server can control what happens
on a call after a Pivot Request.

In this scenario, you have an front Desk Phone that you only want ringing
during normal business hours. After hours, calls should go straight to
voicemail. This example builds a system for handling this using Pivot,
using Pivot TwiML and some of the most commonly used Callflow modules.
To best illustrate how your Server and Pivot might communicate.
{% BRAND_NAME %} provides easier and more powerful ways to build a system like
this, which are discussed [here](#improvements-using-more-of-the-platform).


## Routing to Pivot

Use the [GUI App](../twiml/switchover.md#pivot-app) to route inbound calls to the front desk phone
number to Pivot.
Alternatively, you could use the [Advanced Callflow UI](../twiml/switchover.md#callflows-app)
or the [Callflow HTTP API](../twiml/switchover.md#configure-routing-with-the-callflows-api).

Using the HTTP API this time, you would post this JSON to the API.

```json
{
	"data":
	{
		"name":"Front Desk",
		"numbers":["555-113-0451"],
		"flow": {
			"module": "pivot",
			"data": {
				"voice_url": "http://your.server.com/front-desk"
			}
		}
	}
}
```

## Responding to a Pivot Request

Our server will respond with a different flow depending on the time of day. You can use nearly any
HTTP server technology, so long as it can tell the time and provides conditional logic.

If the time is between 9am and 5pm, your server should respond with this Callflow JSON:


```json
{
  "module": "resource",
  "data": {
    "use_local_resources": true,
    "to_did": "[Front Desk Line]"
  }
}
```

or this Pivot TwiML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>{Another Number}</Dial>
</Response>
```

If the time is outside of business hours, it should respond with this Callflow JSON:

```json
{
  "module": "tts",
  "data": {
    "text": "Please leave your message after the beep"
  },
  "children": {
    "_": {
      "module": "record_caller",
      "data": {
        "url": "http://your.recording.server/voicemail/",
        "time_limit":360
      }
    }
  }
}
```

Or this Pivot TwiML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>Please leave your message after the beep</Say>
    <Record recordingUrl="http://your.recording.server/voicemail/"
            maxLength="360"></Record>
</Response>
```

Both will record the caller, and send that recording to your server.
Your server will then need to handle receiving, storing, and maintaining these recordings.
See [`<Record>` URL](../twiml/record.md#recordingurl) and
[record_call](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/record_call.md).
For more information.

This example is deliberately simple in order to highlight the request and response pattern
between pivot and your server. {% BRAND_NAME %} provides several tools which can make
a system like this easier to build, more powerful, and more flexible.

## Improvements Using More of the Platform

### Create a Device for the Front Desk


Creating a [device](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/devices.md)
will let you use some additional features from {% BRAND_NAME %}'s, and reduces 
the burden on your server by managing what phone numbers belong to what. To create a 
device, see the [API Document](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/devices.md)


Once setup, you can replace the business-hours Callflow JSON with this:

```json
{
  "module": "device",
  "data": { "id": "{FRONT_DESK_DEVICE_ID}" },
}
```

Pivot can access {% BRAND_NAME %} devices when using Pivot TwiML too:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial><Device>{FRONT_DESK_DEVICE_ID}</Device></Dial>
</Response>
```

### Make a Voice Mail Box

Undoubtedly the most difficult portion of this scenario is storing the and managing the recordings.
Setting up a [{% BRAND_NAME %} voicemail box](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/voicemail.md)
eliminates this difficulty, and simplifies the flows your server responds with.

Creating and managing voicemail boxes is covered in the 
[API Document](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/voicemail.md).

Once setup, you can use this voicemail box in the after-hours flow:

```json
{
  "module": "voicemail",
  "data": {
    "id": "{VM_BOX_ID}"
  }
}
```

### Use {% BRAND_NAME %}'s Temporal Routing

{% BRAND_NAME %} provides the [`temporal_route`](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/temporal_route.md)
Callflow, no logic from your server required!

Temporal routes rely on [Temporal Rules](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/temporal_rules.md),
creating and managing these rules is covered in the Temporal Rules
[API Document](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/temporal_rules.md)

Once created, your server can reply with this JSON to every Pivot request for the
front desk:

```json
{
  "module": "temporal_route",
  "data": {
    "rule_set": "{TEMPORAL_RULE_ID}"
  },
  "children": {
    "_": {
      "module": "voicemail",
      "data": {
        "id": "{VM_BOX_ID}" 
      }
    }
    "{TEMPORAL_RULE_ID}": {
      "module": "device",
      "data": {
        "id": "{FRONT_DESK_DEVICE_ID}"
      }
    }
  }
}
```

In fact, the flow that routed these calls to Pivot in the [first place](#routing-to-pivot) could
be replaced with this callflow JSON.