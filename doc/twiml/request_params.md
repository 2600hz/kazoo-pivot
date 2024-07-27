# Pivot TwiML request data parameters
@TODO add descriptions

TwiML format of request data send by Pivot will include the following parameters. You can find their equivalent Kazoo meaning in the table.

| Request Parameter     | Kazoo Name       | Description                                                                          |
| -----------------     | --------------   | --------------------------------------------------------------------------------     |
| `AccountSid`          | `Account-ID`     | Account ID processing the call                                                       |
| `ApiVerson`           | -                | Version string related to API changes, it always sets to `2010-04-01`                |
| `CallSid`             | `Call-ID`        | Unique identifier of the call leg                                                    |
| `CallStatus`          | -                | What state the call is currently in                                                  |
| `CallerName`          | `Caller-ID-Name` | Name of the caller, if any                                                           |
| `Direction`           | `Direction`      | Direction of the call (outbound if Kazoo originated the call, inbound otherwise)     |
| `From`                | `From-User`      | Caller's number, if available                                                        |
| `To`                  | `To-User`        | Dialed number                                                                        |

@TODO call-status

### Optional/Conditional Request Data

| Request Parameter     | Kazoo Name            | Description                                           |
| -----------------     | -------------------   | ----------------------------------------------------- |
| `DialCallDuration`    | `Billing-Seconds`     | How many billable seconds the call lasted             |
| `DialCallSid`         | `Other-Leg-Unique-ID` | Call-ID of the b-leg                                  |
| `DialCallStatus`      | -                     | Call status of the b-leg                              |
| `Digits`              | `DTMF-Pressed`        | The DTMF(s) (touch tone) pressed by the caller        |
| `RecordingDuration`   | `Recording-Duration`  | Length of the recording, if available                 |
| `RecordingSid`        | `Media-Name`          | Name of the recording file                            |
| `RecordingUrl`        | `Recording-URL`       | Where a recording will be sent (via HTTP PUT request) |
| `TranscriptionSid`    |                       |                                                       |
| `TranscriptionStatus` |                       |                                                       |
| `TranscriptionText`   |                       |                                                       |
| `TranscriptionUrl`    |                       |                                                       |
| `TranscriptionUrl`    |                       |                                                       |

Other optional data includes user-defined key/value pairs stored using the <Set> verb below.
