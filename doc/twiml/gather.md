# \<Gather>  
Get input from callers

## Overview  

`<Gather>` digits by DTMF dial tones from callers. `<Gather>` Completes when the `timeout` is 
reached, the caller provides the expected `numDigits`, or when the caller presses the 
`finishOnKey`. Pivot will _always_ make an `action` request upon complletion if the caller 
provides digits. Otherwise, Pivot will render the next term of the document.  

## Attributes

| Name                          | Values Description         | Default value     |
|-------------------------------|----------------------------|-------------------|
| [`action`](#action)           | URL                        | Current Pivot URL |
| [`finishOnKey`](#finishOnKey) | any single DTMF character  | `#`               |
| [`method`](#method)           | `GET` \| `POST`            | `GET`             |
| [`numDigits`](#numDigits)     | positive integer           | infinite          |
| [`timeout`](#timeout)         | positive integer (seconds) | 5                 |


### `action`
The URL where Pivot will make an http request if `<Gather>` completes with collected digits. 
(`method`)[#method] sets the http method of the request. Pivot expects another Pivot document in 
response as either Kazoo JSON or TwiML, which will be rendered. Pivot never continues execution of 
 the current document after an `action`, further terms will be unreachable. 

Unlike most other Pivot TwiML terms, `<Gather action="">` defaults to to the current Pivot URL,
rather than `none`.

### `finishOnKey`
`<Gather>` will complete when this key is pressed and pivot will make an Action Request. Defaults to `#`

### `method`
sets the http request method for the (`action`)[#action] request, either `POST` or `GET`. Pivot 
expects a response with more call instructions. See [Requests](./../requests.md) for information 
about the request and respose formats.  
Defaults to `GET`.

### `numDigits`
Maximum number of digits Pivot will collect. Once the maximum is reached, Pivot will immediately 
make an `action` request.  

### `timeout`
Maximum amount of time pivot will wait before `<Gather>` completes. Defaults to 5s

## Nestable terms
Pivot allows some terms to be executed during `<Gather>`. Execution of these terms stops as soon 
as Pivot recieves DTMF tones.  

| Noun                    | Description              |
|-------------------------|--------------------------|
| [`<Play>`](./play.md)   | Play media               |
| [`<Pause>`](./pause.md) | Pause media playback     |
| [`<Say>`](./say.md)     | speak to a caller by TTS |

## Examples
