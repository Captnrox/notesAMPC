Simplest communication primitive in [distributed](.md) cryptographic protocols, which works by creating a [point-to-point](.md) communication link. Working correctly, it ensures two properties:

- **Privacy**:  Only the intended recipient can read the message, achieved with [encryption](.md).
- **Integrity**: The recipient can verify that the message came from the claimed sender and that it wasn't intercepted or modified by an [eavesdropper](.md). This works by using [MACs](.md) or [digital signatures](.md). 
  
As they are [point-to-point](point-to-point.md) they only ensure that each message is sent to one participant.

In [Secret Sharing](Secret Sharing.md) these are used to ensure that the dealer can send to each participant their shares privately. They do not ensure consistency between different recipients.