# \<Hangup>

hangup calls

## Overview

Hangup the current call and end the Pivot session with `CallStatus=completed`.  
If this is the only term in a Pivot TwiML document, the call will be answered and immediately hung 
up, and the {% BRAND_NAME %} account will be charged for the call. [`<Reject>`](reject.md) may be preferable 
in some cases to avoid unnecessary charges.

For the [Callflow Json](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/README.md) 
equivalent, see [response](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/response.md).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>Thank you for calling, Goodbye</Say>
    </Hangup>
</Response>
```

## Attributes

`<Hangup>` does not support any additional attributes.
