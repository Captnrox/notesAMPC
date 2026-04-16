Fundamental consensus problem where the processes, each with an **initial value**, must **agree on a single value** despite the Byzantine (malicious) failures. 

BA ensures:

- **Validity**:  If all correct process start with the value v, then all correct processes decide on that value. (weak validity)
	- **Termination:** Every correct process eventually decides on something. No correct process waits forever.
- **Agreement**: No two correct processes decide differently
- **Integrity**: Messages are **authenticated** no message sent can modified, messages received are the ones sent.

# How it works
---
1.  Commander phase - Designate a commander using [commander selection protocols](Commander%20Selection.md) that broadcasts their value to all other processes.
2. Relay phase - Recursively have other processes forward received value to everyone else for t+1 rounds.
3. Decision phase - After collecting all received values a **majority function** is applied on the messages and the value decided is the output from the function.

# Thresholds
---
For the agreement to work, it requires **n** parties on the system. Having **t** byzantine parties, we must have **n>3t**. In order to outnumber the malicious nodes in the decision path, we need **t+1** communication rounds.

# Guarantee
---
It ensures all honest parties reach **identical decisions** even when some participants lie or behave arbitrarily, this only stands in synchronous networks, the only way for it to be deterministic. The honest nodes still agree on a value even if the commander picked is malicious.