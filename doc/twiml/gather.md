# \<Gather>  

Get input from callers

## Overview  

`<Gather>` digits by DTMF dial tones from callers. `<Gather>` Completes when the `timeout` is 
reached, the caller provides the expected `numDigits`, or when the caller presses the 
`finishOnKey`. Pivot will _always_ make an `action` request upon completion if the caller 
provides digits. If Pivot does not receive any DTMF (touch tone) digits, it will render the next term
in the document.

For the [Callflow Json](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/README.md) equivalent, see 
[`collect_dtmf`](https://docs.2600hz.com/git/2600hz/kazoo-callfow/doc/collect_dtmf.md). 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Gather/>
  <Say>The caller will only hear this if they do not input DTMF</Say>
</Response>
```

## Attributes

| Name                          | Values Description             | Default value     |
|-------------------------------|--------------------------------|-------------------|
| [`action`](#action)           | URL                            | Current Pivot URL |
| [`finishOnKey`](#finishOnKey) | any single DTMF character      | `#`               |
| [`method`](#method)           | `GET` \| `POST`                | `GET`             |
| [`numDigits`](#numDigits)     | any positive integer           | infinite          |
| [`timeout`](#timeout)         | any positive integer (seconds) | 5                 |


### `action`

The URL where Pivot will make an HTTP request if `<Gather>` completes with collected digits. 
(`method`)[#method] sets the HTTP method of the request. Pivot expects another Pivot document in 
response as either {% BRAND_NAME %} JSON or TwiML, which will be rendered. Pivot never continues execution of 
the current document after an `action`, further terms will be unreachable. 

Unlike most other Pivot TwiML terms, `<Gather action="">` defaults to to the current Pivot URL,
rather than `none`.

### `finishOnKey`

`<Gather>` will complete when this key is pressed and pivot will make an Action Request. Defaults to `#`

### `method`

Sets the HTTP request method for the (`action`)[#action] request, either `POST` or `GET`. Pivot 
expects a response with more call instructions. See [Requests](./../request_params.md) for information 
about the request and response formats.  

Defaults to `GET`.

### `numDigits`

Maximum number of digits Pivot will collect. Once the maximum is reached, Pivot will immediately 
make an `action` request.  

### `timeout`

Maximum amount of time pivot will wait before `<Gather>` completes. Defaults to 5s

## Nestable terms

Pivot allows some terms to be executed during `<Gather>`. Execution of these terms stops as soon 
as Pivot receives DTMF tones.  

| Noun                    | Description              |
|-------------------------|--------------------------|
| [`<Play>`](./play.md)   | Play media               |
| [`<Pause>`](./pause.md) | Pause media playback     |
| [`<Say>`](./say.md)     | speak to a caller by TTS |


## Examples

[`<Redirect>`](./redirect.md) and `<Gather>` can be used together to branch a call differently
if the caller does not provide DTMF:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Gather action="your.server.com/account_number">
        <Say>
            Please enter your account number,
            followed by the pound sign
        </Say>
    </Gather>
    <Redirect method>
        your.server.com/timeout
    </Redirect>
</Response>
```