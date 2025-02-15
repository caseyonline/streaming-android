#Adding security to Red5 Pro streams
While a lot of streaming applications are intended to be as public as possible, many are intended for more descrete audiences - or decidedly confidential. For those situations, SRTP can give a piece of mind that traffic can't be spyed on uninvited.

**NOTE:** SRTP Support requires Red5 Pro Server version 5.5.0 or higher

###Example Code
- ***[PublishTest.java](../PublishTest/PublishTest.java)***
- ***[PublishEncryptedTest.java](PublishEncryptedTest.java)***


##SRTP
The Red5 Pro SDK has included the option to wrap its streams in `SRTP` - a Secure variation of the `RTP` (Real-time Transport Protocol) that is normally used to wrap `RTSP` (Real-time Streaming Protocol) that the SDK operates on. Specifically, once negotiation with the server is complete, everything sent between the SDK and server (or vice versa) is encrypted and authenticated to ensure that a third party can't view the stream or modify data without detection.

Also, as part of the SRTP specification, we support "null key" SRTP. Refered to in the sdk as `Null SRTP` - it's essentially taking the authentication protection to prevent outside tampering with the stream in situations that don't require the extra overhead for the view-protection of encryption.

Note that while the encryption suite used is relatively fast where cryptograhpy is concerned, it does still introduce some overhead. Even Null SRTP still requires processing all data before transmition, and mobile devices aren't the most powerful of computers. This overhead will scale roughly with bitrate settings, and may lower the framerate of a stream.

###Enable Encryption
As complicated as encryption is, it's implementation couldn't be simpler - simply set the `protocol` property of the R5Configuration (the first value of its constructor) to the correct value before initializing the connection and the SDK will take care of the rest.

```
Java
R5Configuration config = new R5Configuration(R5StreamProtocol.SRTP,
```
<sup>
[PublishEncryptedTest.java #19](PublishEncryptedTest.java#L19)
</sup>

To set Null SRTP instead, use `NULL_SRTP` - and of course, the default of `RTSP` is still available where security isn't such a concern.

###New Events
As part of the new protocols, two new events have been added to differentiate between an issue specific to the SRTP setup and the SDK in general.

`R5ConnectionEvent.SRTP_KEY_GEN_ERROR` - This indicates that something has prevented the device from generating its part of the key exchange. The chance of seeing this error should be zero - barring a manufacturing fault or software malfunction.

`R5ConnectionEvent.SRTP_KEY_HANDLE_ERROR` - This indicates that there has been a fault between the server and the SDK. Either that there's been some fault that prevented the key exchange from happening cleanly - or that the server is using an old version and doesn't support SRTP.

For both of these errors, the message that accompanies them will include more detail on what exactly hasn't worked.
