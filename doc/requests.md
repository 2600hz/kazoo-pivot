# Pivot Request Payload

Pivot can encode the request payload to a developer server in a query string when configured as `GET`, or in the request body when configured as `POST`.

[//]: # (@TODO document how to configure pivot `req_format`)

## Payload

Some fields may not be included in every request.

| Field              | Description                                  |
|--------------------|----------------------------------------------|
| Call-ID            | The unique call identifier                   |
| Account-ID         | The account id receiving the call            |
| From               | The SIP From username                        |
| From-Realm         | The SIP From realm                           |
| To                 | The SIP To username                          |
| To-Realm           | The SIP To realm                             |
| Request            | The SIP Request username                     |
| Request-Realm      | The SIP Request realm                        |
| Call-Status        | Status of the call                           |
| Api-Version        | The version of the API                       |
| Direction          | The direction of the call, relative to Kazoo |
| Caller-ID-Name     | Caller ID Name                               |
| Caller-ID-Number   | Caller ID Number                             |
| User-ID            | The Kazoo User identifier(s) of the caller   |
| Language           | The caller's language preference             |
| Recording-Url      | Where the recording will be stored           |
| Recording-Duration | How long the recording is                    |
| Recording-ID       | The recording id                             |
| Digits             | Any DTMF (or collection of DTMFs) pressed    |
