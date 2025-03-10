# \<Conference>

Connect callers to a multi-party conference call using TwiML Dial Conference capabilities.

## Overview

`<Conference>` allows you to connect the current call to a multi-party conference. Participants can join and leave at any time, and the conference continues until the specified conditions are met.

## Attributes

`<Conference>` noun has the following attributes:

| Name | Description | Default value |
|------|-------------------|---------------|
| [`id`](#id) | Conference doc _id that was created by Conference app from monster-ui |               |
| [`startConferenceOnEnter`](#startconferenceOnenter) | Whether the conference should start when this caller joins. This participant is moderator |               |
| [`endConferenceOnExit`](#endconferenceonexit) | Whether the conference should end when this caller leaves. This participant is moderator |               |
| [`endConferenceOnLastMemberExit`](#endconferenceOnLastMemberExit) | Whether the conference should end when the last member leaves |               |
| [`maxParticipants`](#maxparticipants) | Maximum number of participants allowed in the conference | `250`         |
| [`beep`](#beep) | Whether to play a beep when participants join or leave |               |
| [`muted`](#muted) | Whether the participant joins muted | `false`       |
| [`deaf`](#deaf) | Whether the participant joins deaf | `false`        |

### `id`

References a conference document _id that was created using the Conference app from monster-ui. When provided, the conference will use the settings from this document.

### `startConferenceOnEnter`

This attribute controls whether the conference will start when this participant joins. When set to `true`, the conference begins and other participants who were waiting will be joined. When set to `false`, this participant will be put on hold until a participant with `startConferenceOnEnter="true"` joins.


### `endConferenceOnExit`

This attribute determines whether the conference should end when this participant leaves. When set to `true`, the conference will end and all other participants will be disconnected when this participant exits. When set to `false`, the conference continues after this participant leaves.

### `endConferenceOnLastMemberExit`

This attribute determines whether the conference should end when the last non-moderator member leaves. When set to `true`, the conference will end when the last member exits, even if moderators are still present.

### `maxParticipants`

The maximum number of participants allowed in the conference. Once this number of participants is reached, additional participants will not be able to join until someone leaves. Maximum value is 250.

### `beep`

Controls whether a beep sound is played when participants join or leave the conference. Possible values are `true`, `false`, `onEnter`, or `onExit`.

### `muted`

When set to `true`, the participant joins the conference muted. Default is `false`.

### `deaf`

When set to `true`, the participant joins the conference deaf (unable to hear others). Default is `false`.

## Nesting

`<Conference>` must be nested inside a `<Dial>` verb.

## Examples

### Basic Conference

Here is a simple example of creating a conference:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Dial>
    <Conference
      startConferenceOnEnter="true"
      endConferenceOnExit="false"
      maxParticipants="10"
    >
      MyTwimlConference
    </Conference>
  </Dial>
</Response>
```

This instructs Kazoo to place the caller into a conference named "MyTwimlConference" that will start when this participant enters (assuming this participant is a moderator). The conference can hold up to 10 participants and will not end when this participant exits.

### Conference with Recording

To enable conference recording, you need to follow these steps:

1. **Create a static conference** in your account using the Conference Monster UI app.
2. **Configure your recording settings** in the account database.
3. **Reference the conference in your TwiML** using the conference document ID.

> **Note:** While recording is configured to start automatically, the actual recording will only begin when at least 2 participants have joined the conference. This behavior is built into the system to prevent recording when only one person is waiting, which helps avoid unnecessary recordings.

#### Step 1: Modify your conference document

From your conference document in your account database with your preferred settings from monster-ui, add `profile_name` and `recording_url`.

The `profile_name` is optional. It will be included in request queries when uploading the file to your server.

The `recording_url` field is also optional and allows you to store recordings on your own server. If it is not set, recordings will be stored in your database.

The record file has a `.wav` format

```json
{
  "_id": "c5b71bdf8c3d3ee895c79339150c5f8a",
  "_rev": "9-74deb3060d3b14b25c80964c80ed00b4",
  "name": "Conference with Recording",
  "owner_id": "5c61853e23380d1fd281b044dde9d0ba",
  "profile_name": "twiml",
  "recording_url": "http://your-server.com/recordings/upload"
}
```

#### Step 2: Configure Conference Settings

Create or update the `configs_conferences` document in your account database:

```json
{
  "_id": "configs_conferences",
  "_rev": "18-ee740db677fa034690d4b0fd1950b5b6",
  "profiles": {
    "twiml": {
      "auto-record": true
    }
  }
}
```

This example creates a custom profile named `twiml` with recording enabled.

#### Step 3: Reference in TwiML

Finally, reference your conference in the TwiML using the conference document ID:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Dial>
        <Conference
            startConferenceOnEnter="true"
            id="c5b71bdf8c3d3ee895c79339150c5f8a"
            beep="true"
        >
            My Recorded Conference
        </Conference>
    </Dial>
</Response>
```


