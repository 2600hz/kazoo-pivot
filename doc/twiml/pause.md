# \<Pause>

This verb instruct Pivot to wait for specified amount if seconds before continuing
processing the rest of TwiML document. The caller waits in silnce for the period of pause.

You account still will be billed for any amount of wait your pausing the call for.

## Attributes

| Name | Values Description | Default value |
|------|--------------------|---------------|
| [`length`](#length) | a positive interger representing seconds to wait | 1 second |

### `length`

The amount of time in seconds to pause the call before conotinuing processing the rest of TwiML document.

Minimum value is `1` and maximum value is `3600` (1 hour).

## NEesting

You cannot nest anything within this verb. This verb can only be nested within
[`<Gather>`](./gather.md).

## Examples
