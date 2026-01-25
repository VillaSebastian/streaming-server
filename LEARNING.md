Video is not really a stack of full pictures one after another, compression needs to exist to avoid enormous payloads. 

Codects compress this by using:
- I-frame (keyframe) -> Complete image, acts as reset point
- P-frame (predicted frame) -> Only stores the difference from a previous frame
- B-frame (bi-directional frame) -> References past and future frames

A GOP is:
One I-frame followed by the P/B-frames that depend on it.

I  P  B  P  B  P  B  P

2 seconds GOP -> One keyframe every 2 seconds

Short GOP
- More keyframes
- Larger bitrate
- Faster startup
- Better error recovery
- Lower latency

This creates the need to chose between different architectures: Browser video players vs WebRTC