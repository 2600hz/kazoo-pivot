# \<Device>

`<Dial>` {% BRAND_NAME %} devices

## Overview 
`<Dial>` will bridge the call to the provided {% BRAND_NAME %} `<Device>`

`<Device>` expects a valid 32 character alphanumeric device ID configured in the {% BRAND_NAME %} account, see 
the [Devices](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/devices.md) article for more 
information.

## Attributes

`<Device>` does not support any additional attributes.

## Examples

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>
        <Device>a5kdh6m39vh6k3lfhn6k3420dkn67l2</Device>
    </Dial>
    <Say>Hello and Goodbye</Say>
</Response>
```

If the Device answers, both the caller and the device will hear "Hello and Goodbye".