# Pivot Request Payloads

Pivot can send requests in two formats, and two parameter styles


## Payload

These parameters are sent in every request.  

| Kazoo Callflow Parameters | Pivot TwiML Parameters | Description                                  |
|---------------------------|------------------------|----------------------------------------------|
| Call-ID                   | CallSid                | The unique call identifier                   |
| Account-ID                | AccountSid             | The account id receiving the call            |
| Call-Status               | CallStatus             | Status of the call                           |
| Api-Version               | ApiVerson              | The version of the API                       |
| Direction                 | Direction              | The direction of the call, relative to Kazoo |
| Caller-ID-Name            | CallerName             | Caller ID Name                               |
| Caller-ID-Number          | CallerNumber           | Caller ID Number                             |
| User-ID                   |                        | The Kazoo User identifier(s) of the caller   |
| Language                  |                        | The caller's language preference             |
| Recording-Url             |                        | Where the recording will be stored           |
| Recording-Duration        |                        | How long the recording is                    |
| Recording-ID              |                        | The recording id                             |
| Digits                    |                        | Any DTMF (or collection of DTMFs) pressed    |

### Optional Payload
These parameters are sent in some requests.  

| Kazoo Callflow Parameters | Pivot TwiML Parameters | Description              |
|---------------------------|------------------------|--------------------------|
| From-User                 | From                   | The SIP From username    |
| From-Realm                |                        | The SIP From realm       |
| To                        | `To`                   | The SIP To username      |
| To-Realm                  |                        | The SIP To realm         |
| Request                   |                        | The SIP Request username |
| Request-Realm             |                        | The SIP Request realm    |


Pivot can encode the request payload to a developer server in a query string when 
configured as `GET`, or in the request body when configured as `POST`.

[//]: # (@TODO document how to configure pivot `req_format`)

## Payload

