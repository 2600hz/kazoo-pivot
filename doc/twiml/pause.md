# \<Pause>

`<Pause>` Pivot rendering

## Overview

Pivot will wait for `length` seconds before rendering the next term of the Pivot document. 
The caller waits in silence for the period of pause.  

Calls are still active while Pivot is rendering a `<Pause>` term, so the {% BRAND_NAME %} account will
still be billed for the paused period.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>This call will continue for 3 more seconds</Say>
    <Pause length="3"></Record>
    <Say>Goodbye</Say>
</Response>
```

## Attributes

| Name                | Values Description         | Default value |
|---------------------|----------------------------|---------------|
| [`length`](#length) | positive integer (seconds) | 1             |

### `length`

The time in seconds to pause the call before Pivot renders the next term. Minimum value is `1` 
and maximum value is `3600` (1 hour).  
Defaults to 1 second.

## Nesting

Can be nested under [`<Gather>`](./gather.md) to pause between sibling terms. Terms cannot be 
nested within `<Pause>`. 

