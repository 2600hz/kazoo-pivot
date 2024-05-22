# \<Redirect>

Redirect to another Pivot flow

## Overview  

`<Redirect>` will cause another Pivot HTTP request to the url in it's body. Pivot expects another 
Pivot document in response as either {% BRAND_NAME %} JSON or TwiML, which will be rendered. Pivot will not 
continue execution of the current document after a `<Redirect>`, further terms will be unreachable.  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say>Requesting another, better flow than this</Say>
    <Record>your.server.com/better</Record>
    <Say>This is unreachable</Say>
</Response>
```


[//]: # (@TODO instead of URL in content, people can set multiple `<Variable>` to set on the call.)

## Attributes

| Name                | Values Description | Default value |
|---------------------|--------------------|---------------|
| [`method`](#method) | `GET` \| `POST`    | `GET`         |

### `method`

Sets the HTTP request method for the `<Redirect>` request, either `POST` or `GET`. Pivot 
expects a response with more call instructions. See [Request Paramterss](../request_params.md) for information 
about the request parameters.

