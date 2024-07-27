# \<Sip>
`<Dial>` a SIP endpoint

## Overview
Pivot will make a sip route invitation to the SIP URI in the body of this term.

### SIP URIs
There are many resources available documenting and explaining SIP protocol. A brief explaination
of SIP Uniform Resource Identifiers (URIs) is provided here for convinience. 

Basic URIs contain at least the user and the host, and look like email addresses starting wtih 
`sip:`. the user precedes the @, and the host follows:
```<sip:asmith@2600hz.com>```
Often in Kazoo, `display names` and `ports` are specified:
```"Aspen Smith" <sip: asmith@2600hz.com:5060>```
URIs may also include additional parameters. For example, Kazoo frequently uses `transport` 
to specify the transport layer protocol to use.
```"Aspen Smith" <sip: asmith@2600hz.com:5060;transport=tcp>```
Extra parameters can be specified at the end of the uri:
```"Aspen Smith" <sip: asmith@2600hz.com:5060;transport=tcp>;expires=3600```

## Attributes

No attributes

## Examples
