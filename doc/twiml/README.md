# Pivot TwiML

Pivot supports a subset of TwiML to help ease you into Pivot with an existing TwiML-based application.  

To use this Pivot TwiML a developer server must respond to Pivot requests with `Content-Type:
application/xml` HTTP header and a valid TwiML document.  

Pivot TwiML is an xml scripting language for routing and controlling calls in Kazoo. 
Documents begin with an XML declaration and have one root element, `<Response>`.  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <!-- your instructions goes here -->
</Response>
```

For example, a developer server could respond to a Pivot request with instructions to play media 
stored with the kazoo account:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Play>ad303920d65b486f1ba0e2c3fe274418</Play>
</Response>
```

Pivot TwiML elements are called **terms**. Pivot renders terms one at a time from top to bottom, 
waiting for each term to complete before beginning to render the next term. Some terms have special
nested behaviors, see the documentation for individual terms for more information.


## Pivot TwiML Terms

Fewer of Kazoo's suite of call actions and features are available using Pivot TwiML than Pivot 
Callflow Json.

| Verb                          | Description                                               | Nestable terms                                                                                                                                         |
|-------------------------------|-----------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`<Dial>`](./dial.md)         | Connect the caller to other endpoints                     | plain text numbers, [`<Conference>`](./dial/conference.md), [`<Number>`](./dial/number.md), [`<User>`](./dial/user.md), [`<Device>`](./dial/device.md) |
| [`<Record>`](./record.md)     | Record the caller                                         |                                                                                                                                                        |
| [`<Gather>`](./gather.md)     | Collect DTMFs from the caller                             | `<Play>`, `<Say>`                                                                                                                                      |
| [`<Play>`](./play.md)         | Play a media file (mp3, wav) to the caller                |                                                                                                                                                        |
| [`<Say>`](./say.md)           | Use a TTS engine to say supplied text                     |                                                                                                                                                        |
| [`<Redirect>`](./redirect.md) | Like an HTTP Redirect, make another HTTP Pivot request    | `<Variable>`                                                                                                                                           |
| [`<Pause>`](./pause.md)       | Pause callflow execution for a number of seconds          |                                                                                                                                                        |
| [`<Hangup>`](./hangup.md)     | Hangup the call                                           |                                                                                                                                                        |
| [`<Reject>`](./reject.md)     | Reject (without answering - won't start billing) the call |                                                                                                                                                        |
