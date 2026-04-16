Cornerstone in asynchronous secure protocols whose objective is to enable honest parties of an asynchronous protocol to agree on a common subset **C**, in order to avoid endless waiting for all network members. All members of the subset adhere to a property **Q** to be defined in order to filter the nodes of interest. 

ACS ensures:

- **Validity**: Every honest party will eventually satisfy Q and/or have a set of other parties that do so, meaning all honest parties will have C and terminate.
	- **Termination**: All honest parties eventually agree on C, terminating the protocol.
- **Correctness**: If an honest party agrees on P belonging to C, then all honest parties do so, ending up with all honest parties agreeing on the same C.


# How it works
---
1. **Voting**: For each party in the system initiate or participate in an [ABA](Asynchronous Byzantine Agreement.md) instance for that party. If for the node, the party respects the desired property than participation should be with a 1. And the voting instance will decide if the party belongs on the core set or not.
2. **Awaiting**: Wait until n-t [ABA](Asynchronous Byzantine Agreement.md) instances reach a output of 1. When they do so, enter 0 in all remaining instances.
3. **Termination**: Output a set **Si** containing all the all the indices **j** that correspond to the instances that output 1, and terminate.

# Thresholds
---
The core set will end up with a size of n-t, where all members satisfy Q, and t is the number of adversaries on the network. n>3t or n>4t depends on weather the AMPC is statistical or perfect.
# Guarantee
---
Running ACS will ensure all parties or at least all honest parties know and agree on a verifiable and sufficient subset of nodes that work and collaborate, preventing having to deal with indefinite waiting and other issues.