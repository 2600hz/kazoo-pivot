# \<Hangup>
hangup calls

## Overview
Hangup the current call and end the Pivot session with `CallStatus=completed`.  
If this is the only term in a Pivot TwiML document, the call will be answered and immediately hung 
up, and the Kazoo account will be charged for the call. [`<Reject>`](reject.md) may be preferable 
in some cases to avoid unnecessary charges.

## Attributes

`<Hangup>` does not support any additional attributes.
