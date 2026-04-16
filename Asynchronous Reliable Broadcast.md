Public communication primitive where one sender broadcasts a message and all parties, or at least the honest ones (usually prioritized/prefered), receive the same message, specially if the sender is honest. It strives for **consistency** and **guaranteed** eventual delivery. (Byzantine ARB)

ARB ensures:

- **Validity**: If the **sender is honest** and broadcasts m, then **every honest** participant eventually receives m and delivers it to the client.
	- **Termination**: After a message m is broadcast, eventually the process ends, all **honest** participants will eventually deliver.
- **Agreement**: If any **honest** participant deliver m, then **all honest** participants will eventually deliver m.
- **Integrity**: No **honest** participant delivers a message the sender did not broadcast.
- **No duplication**: no message is delivered more than once to a recipient, even if re-transmissions or failures occur.

## How it works
---

1. SEND phase - The **designated sender broadcasts** a message to everyone, either directly or through point-to-point channels.
2. ECHO phase - **Receiving party** checks  the validity of the message and then sends an **echo message** to **all parties**. **Every party** is forwarding the **sender's message** to **everyone** else.
3. READY phase - When a **party receives** at least **(n+t)/2 echo messages** from **distinct** parties, where **t** is the **faulty parties** threshold it sends a **ready message** to **everyone**. Once a party receives **enough ready messages**, at least **2t+1**, then the message **m is delivered**.

## Thresholds
---
System assumes n parties, t < n/3 Byzantine ones. The thresholds **(n+t)/2 echoes** and **2t+1 readies** ensure no honest party diverges, no forced delivery of fake message, and propagation eventually works. 
ARB needs n > 2f to avoid crash faults and n > 3t for Byzantine adversaries.

## Guarantee
--- 
Even if the sender **crashes** **all honest parties** agree and deliver on **one value** and no **honest party** gets stuck if the others progress. The redundancy allows it to "resend"/echo the messages ensuring that even if the sender crashes the whole network knows m. It also handles up to **t byzantine faults** and works on an assumed asynchronous network.

## Use Case
---
