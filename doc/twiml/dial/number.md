# \<Number>

`<Dial>` phone numbers

## Overview

`<Number>` allows simultaneous dialing of up to ten phone numbers. Pivot will connect the call to 
the first number that answers, the others will be hung up. `<Dial>` can also use a single 
`<Number>` attribute.

## Attributes

`<Number>` does not support any additional attributes.

## Examples

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>
        <Number>
            555-123-4567
        </Number>
        <Number>
            555-113-0451
        </Number>
        <Number>
            610-987-1597
        </Number>
    </Dial>
</Response>
```

555-123-4567, 555-113-0451, and 610-987-1597 will ring simultaneously. Whichever
answers first will be connected to the original caller, the rest will be hung up.