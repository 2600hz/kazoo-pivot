# \<Pause>

`<Pause>` 
Pivot will wait for `length` seconds before rendering the next term of the Pivot document. 
The caller waits in silence for the period of pause.  

Calls are still active while Pivot is rendering a `<Pause>` term, so the Kazoo account will
still be billed for the paused period.  

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

