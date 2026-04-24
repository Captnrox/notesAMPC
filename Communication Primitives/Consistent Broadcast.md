Consistent Broadcast is a public communication primitive that ensures all honest parties that deliver the message, deliver the same message from the sender.

CB ensures:
 - **Consistency**(No-Equivocation): If an **honest party** deliver a message m, then **all honest parts that deliver** must deliver m.
 - **Integrity**: No **honest** participant delivers a message the sender did not broadcast.

## How it works
---

1. Sender broadcasts a signed message with its signature.
2. Each party verifies the signature and if it passes, deliver the message.

## Threshold
---
There is no agreement so there are no thresholds, it works for all f < n. 
## Guarantee
---
Weaker guarantee that ensures **no equivocation**, that is, once a party receives a message, **all parties that deliver** one will **deliver the same** message, however, **no termination** is guaranteed. There is **no agreement** either, some honest parties might get the message while others don't.
## Use Case
---
