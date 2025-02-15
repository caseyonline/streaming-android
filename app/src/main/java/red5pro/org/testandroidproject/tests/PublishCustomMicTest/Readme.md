#Custom Microphone Publishing

This example demonstrates passing custom video data into the R5Stream.

###Example Code
- ***[PublishTest.java](../PublishTest/PublishTest.java)***
- ***[PublishCustomMicTest.java](PublishCustomMicTest.java)***

###Setup
To view this example, you simply need to open the example and subscribe to your stream from a second device.  All video will be recorded, and the microphone audio will have its volume modified before being sent out.

####Attach a Custom Audio Source
Instead of using an R5Microphone, this example uses a custom video source, the `gainWobbleMic`. This increases and decreases the gain between double volume to muted and back. It does this by extending the R5Microphone class and intercepting the `processData` method.

This method recieves a byte array of raw audio samples - each byte being a single, mono sample - and a timestamp in milliseconds with the 0 point being the start of the stream. The array is passed by reference, so it needs to be modified in place - by assigning new values to items in the array, and not assiging a new array - which would just overwrite the reference.

```Java
int s;
for(int i = 0; i < samples.length; i++){

  s = (int) (samples[i] * gain);
  samples[i] = (byte) Math.min(s, 0xff);
}
```
<sup>
[PublishCustomMicTest.java #82](PublishCustomMicTest.java#L82)
</sup>

This example amplifies the value of each byte according to the gain value - clamping the value to keep it from wrapping around when being converted back to a byte. This function could be used as a timing device to provide completely separate audio.
