# Pivot Requests
Pivot can send requests to developer servesr with parameters in either the request URL query string 
when `req_body_format` is set to `form`, or in the request body when `req_body_format` is set to `json`.  

## Payload
These parameters are sent in every request. Pivot Twiml style parameters are used when `req_format` is `twiml`, Kazoo Callflow is used otherwise.

| Kazoo Callflow Parameters | Pivot TwiML Parameters | Description                                                                      |
|---------------------------|------------------------|----------------------------------------------------------------------------------|
| Call-ID                   | CallSid                | The unique call identifier                                                       |
| Account-ID                | AccountSid             | The account id receiving the call                                                |
| Api-Version               | ApiVerson              | The version of the API                                                           |
| Direction                 | Direction              | The direction of the call, relative to Kazoo                                     |
| Caller-ID-Name            | CallerName             | Caller ID Name                                                                   |
| Caller-ID-Number          | CallerNumber           | Caller ID Number                                                                 |
| User-ID                   | -                      | The Kazoo User identifier(s) of the caller                                       |
| Language                  | -                      | The caller's language preference                                                 |
| Direction                 | Direction              | Direction of the call (outbound if Kazoo originated the call, inbound otherwise) |
| To                        | To-User                | Dialed number, SIP endpoint or user                                              |
| From                      | From-User              | Caller's number, if available                                                    |
| ToRealm                   | To-Realm               | To SIP realm                                                                     |
| FromRealm                 | From-Realm             | From SIP realm                                                                   |

### Optional Payload

These paramenters may be sent after certain callflow modules or Pivot TwiML terms.  

| Kazoo Callflow Parameters | Pivot TwiML Parameters | Description                                           |
|---------------------------|------------------------|-------------------------------------------------------|
| Digits                    | Digits                 | Any DTMF (or collection of DTMFs) pressed             |
| Call-Status               | CallStatus             | Status of the call                                    |
| Recording-Url             | RecordingUrl           | Where a recording is sent when using external storage |
| Recording-Duration        | RecordingDuration      | How long the recording is                             |
| Recording-ID              | RecordingSid           | The recording id                                      |
| `RecordingSid`            | `Media-Name`           | recording filename                                    |
| `DialCallDuration`        | `Billing-Seconds`      | How many billable seconds in the call                 |
| `DialCallSid`             | `Other-Leg-Unique-ID`  | Call-ID of the b-leg                                  |
| `DialCallStatus`          | -                      | Call status of the b-leg                              |

