# Reliable Broadcast (RB)

A fundamental distributed communication primitive ensuring **all-or-nothing** message delivery across processes, even when senders crash mid-broadcast. It provides **uniform agreement** on message delivery in **synchronous or partially synchronous** systems with crash failures.

RB ensures:

- **Validity**: If the **sender is honest** and broadcasts m, then **every honest** participant eventually receives m and delivers it to the client.
	- **Termination**: After a message m is broadcast, eventually the process ends, all **honest** participants will eventually deliver.
- **Agreement**: If any **honest** participant deliver m, then **all honest** participants will eventually deliver m.
- **Integrity**: No **honest** participant delivers a message the sender did not broadcast.
- **No duplication**: no message is delivered more than once to a recipient, even if re-transmissions or failures occur.

## How it Works
---

### Basic Flooding Algorithm:

1. **Initial Broadcast**:  
   - The **sender** sends the message to **all processes** (including itself).

2. **Receive & Relay**:  
   - When a **process receives** the message **for the first time**:
     - If it's **not the sender**, it **relays** (re-sends) the message to **all processes**.
   - The process then **delivers** the message locally.

## Thresholds 
---
System assumes it tolerates up to f crash faults, as long as n > f.

## Guarantee
---
Even if the sender crashes it ensures all the honest parties deliver the correct m, as long as some process is running correctly is respected. It can guarantee this resistant in synchronous networks, where time outs can be applied, however it has no protection against malicious parties/Byzantine attackers, without timing bounds the protocol might not terminate, time outs can't be used and we can't distinguish from crashed and slow nodes.

## Use Cases
---
