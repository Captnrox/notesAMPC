Solves the fundamental consensus problem where processes must **agree on a single value** despite Byzantine failures in **asynchronous networks** where messages can be delayed arbitrarily. Unlike synchronous BA, deterministic ABA requires circumventing the **[FLP impossibility](FLP impossibility.md)** through randomization, failure detectors, or partial synchrony.

ABA ensures:

- **Validity**:  If all correct process start with the value v, then all correct processes decide on that value. (weak validity)
	- **Termination:** Every correct process eventually decides on something. No correct process waits forever.
- **Agreement**: No two correct processes decide differently
- **Integrity**: Messages are **authenticated** no message sent can modified, messages received are the ones sent.

# How it works
---
### Ben-OR
---
1. Broadcast phase - Every process broadcasts their current estimation to everyone
2. Decision phase - Each process awaits for at least n-t messages with the estimation and depending on the proportions of the results make a decision. If unanimous, then the correct value is the value received, and it decides on that value. If there is a majority, the process decides the value v is the majority value. If there is no majority, a local flip is used.
3. Repetition - If no unanimity is detected, repeat the protocol until its locally found

### Failure Detector-based
---
1. Election phase - Ω is a failure detector that eventually outputs the identity of a single correct process, elected as leader
2. Proposal phase - Leader proposes a value for processes to agree on
3. Vote phase - Processes vote on the value received, if they agree or disagree
4. Count phase - After 2t+1 votes are received they are counted and a decision is made
5.  Re-election phase - If at any time the leader is faulty or suspected of being so Ω detects and elects a new leader and the process restarts.
### Partial Synchrony
---
1. Election phase - The leader node selects a value and broadcasts it
2. Prepare phase - If the other nodes agree and validate the value they broadcast a prepare message
3. Commit phase - Once 2t + 1 prepare messages arrive, the node sends a commit message
4. Decide phase - After 2t +1 commit messages the node decides that value
5. Re-election - If leader is faulty or gets timedout, its replaced and the protocol resumes from last state.
# Thresholds
---
In the more advanced versions all protocols require **n<3t**. The **2t+1** thresholds are used since there are **t** byzantine attackers. The prepare and commit phase on the last protocol ensure safety a majority agrees with a leader and that a majority knows there is a majority.
# Guarantee
---
ABA ensures that distributed systems can reach Byzantine Agreement without relying on timing assumptions, at the cost of either probabilistic termination or eventual synchrony assumptions.