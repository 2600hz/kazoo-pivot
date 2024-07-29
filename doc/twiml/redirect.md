# \<Redirect>

Redirect to another Pivot callflow

## Overview  

`<Redirect>` will cause another Pivot HTTP request to the url in it's body. Pivot expects another 
Pivot document in response as either Kazoo JSON or TwiML, which will be rendered. Pivot will not 
continue execution of the current document after a `<Redirect>`, further terms will be unreachable.  

[//]: # (@TODO instead of URL in content, people can set multiple `<Variable>` to set on the call.)

## Attributes

| Name                | Values Description | Default value |
|---------------------|--------------------|---------------|
| [`method`](#method) | `GET` \| `POST`    | `GET`         |

### `method`

sets the HTTP request method for the `<Redirect>` request, either `POST` or `GET`. Pivot 
expects a response with more call instructions. See [Requests](./../requests.md) for information 
about the request and response formats.  

