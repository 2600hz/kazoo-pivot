# <Number>
Dial a phone number.

`<Number>` allows simulataneous dailing of up to ten phone numbers. Pivot will connect the call to the first number that answers, the others will be hung up.
`<Dial>` can also use a single `<Number>` attribute

## Attributes

No additional Attributes

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

