# TwiML Verb `<Reject>`

This verb will instruct Kazoo to reject the call with specified `reason` and `code` and
optionally play a media before hanging up the call.

This verb doesn't answer the call if the call is not already answered by any previous
verbs. This can be useful to be used as first verb when your application wants to reject
the call without causing your account to be charged for the call.

@TODO description

## Attributes

| Name | Values Description | Default value |
|------|--------------------|---------------|
| [`reason`](#reason) | Use to set call status: `rejected`, `busy` | `rejected` |

@TODO kazoo only
| [`prompt`](#prompt) | | |
@TODO: maybe link to `Specifing the media to play` in ./media.md

### `reason`

## Nesting

cannot nest, won't accept next

## Examples
