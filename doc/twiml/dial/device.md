# \<Device>

`<Dial>` Kazoo devices

## Overview 
`<Dial>` will bridge the call to the provided Kazoo `<Device>`

`<Device>` expects a valid 32 character alphanumeric device ID configured in the Kazoo account, see 
the [Devices](https://docs.2600hz.com/git/2600hz/crossbar/doc/devices.md) article for more 
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
</Response>
```

