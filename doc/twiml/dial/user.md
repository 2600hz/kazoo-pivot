# \<User>

`<Dial>` a configured user

## Overview

{% BRAND_NAME %} will dial every device assigned to the user in the body of this term, and connect the call 
to whichever picks up first.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>
        <User>128d81866e595be608a51e51e03be2ac</User>
    </Dial>
    <Say>Goodbye</Say>
</Response>
```

## Attributes

`<User>` does not support any additional attributes.
