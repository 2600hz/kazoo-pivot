# \<Reject>

Reject calls without answering

## Overview

If `<Reject>` is the first term in a Pivot TwiML document, the call will rejected 
without incurring carrier costs to the {% BRAND_NAME %} account. If the call was already answered 
by previous Pivot terms, `<Reject>` will end the Pivot session.  

For the [Callflow Json](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/README.md) 
equivalent, see [response](https://docs.2600hz.com/git/2600hz/kazoo-callflow/doc/response.md).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    </Reject>
</Response>
```

## Attributes

| Name                | Values Description                   | Default value |
|---------------------|--------------------------------------|---------------|
| [`reason`](#reason) | `rejected`\| `busy`                  | `rejected`    |
| [`prompt`](#prompt) | URL \| {% BRAND_NAME %} media handle | none          |



### `reason`

Pivot will set the call status as `reason`. {% BRAND_NAME %} will determine the appropriate code from 
`reason` when logging call metadata and provide it to the caller when the call is rejected.

[//]: # (So. This call end status stuff. Is there an app that listens for it? Like does this get picked up by anything else and recorded in, say, a call log?)

[//]: # (I also don't know what kind of code this is. Presumably some kind of standardized telecom code.)

### `prompt`

If `prompt` is set to a valid media URL or handle, Pivot will playback that media to callers 
before disconnecting the line without answering the call. 
See [Media API](https://docs.2600hz.com/git/2600hz/kazoo-crossbar/doc/media.md) for information
on uploading and using media files in {% BRAND_NAME %}.
