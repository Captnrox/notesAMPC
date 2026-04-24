
# Network Model
---
[Asynchronous Network](Network/Asynchronous%20Network.md)

# Adversary Model
---
Assumed p to t byzantine fault nodes (n>=3f+1 or n>=4f+1 usually), where n is the total number of protocol participants. These faulty nodes are fully controlled by a probabilistic polynomial-time bounded adversary that once corrupts the party has full access to its memory and makes said party act like a proxy. The adversary may make these nodes arbitrarily misbehave during execution. This is a PPT adversary whose work during runtime scales with the size of the communication system.

Regardless of the corruption method an adversary may act through static or dynamic corruptions. In static corruption the nodes to corrupt are decided by the adversary before execution. In dynamic, said choice is made during execution based on the internal states of the already corrupted nodes.

*Guo, B. et al. "From Speeding Dumbo: Pushing Asynchronous BFT Closer to Practice"*
*Yurek, T. et al. "Long Live The Honey Badger: Robust Asynchronous DPSS and its Applications"*

# Ideal World
---

# Others
---
[MPCSaaS](MPCaaS/MPCSaaS.md)

# AVSS
---
[Polynomial Interpolation](Encoding-Decoding%20Primitives/Polynomial%20Interpolation.md)
[Commitment schemes](Commitment%20Primitives/Commitment%20schemes.md) 
[Private Authenticated Channel](Communication%20Primitives/Private%20Authenticated%20Channel.md)
[On-line Error Correction](Encoding-Decoding%20Primitives/On-line%20Error%20Correction.md)
[Asynchronous Reliable Broadcast](Communication%20Primitives/Asynchronous%20Reliable%20Broadcast.md)
[(n,t)-Star](Agreement%20Primitives/%28n%2Ct%29-Star.md)

# Input Decision
---
[Agreement on Core Subset](Agreement%20Primitives/Agreement%20on%20Core%20Subset.md)
# Circuit Evaluation
----
[Degree Reduction](Degree%20Reduction.md)
[Linear Operations](Linear%20Operations.md)
[Non-linear Operations](Non-linear%20Operations.md)

# Core Algebraic / Coding Primitives
---
[Polynomial Interpolation](Encoding-Decoding%20Primitives/Polynomial%20Interpolation.md)
[Reed Solomon](Encoding-Decoding%20Primitives/Reed%20Solomon.md)
[On-line Error Correction](Encoding-Decoding%20Primitives/On-line%20Error%20Correction.md)

# Communication and Broadcast Primitives
---
[Private Authenticated Channel](Communication%20Primitives/Private%20Authenticated%20Channel.md)
[Consistent Broadcast](Communication%20Primitives/Consistent%20Broadcast.md)
[Reliable Broadcast](Communication%20Primitives/Reliable%20Broadcast.md)
[Asynchronous Reliable Broadcast](Communication%20Primitives/Asynchronous%20Reliable%20Broadcast.md)

# Agreement / Asynchronous Consensus Primitives
---
[Byzantine Agreement](Agreement%20Primitives/Byzantine%20Agreement.md)
[Asynchronous Byzantine Agreement](Agreement%20Primitives/Asynchronous%20Byzantine%20Agreement.md)
[Asynchronous Multivalue Byzantine Agreement](Agreement%20Primitives/Asynchronous%20Multivalue%20Byzantine%20Agreement.md)
[Agreement on Core Subset](Agreement%20Primitives/Agreement%20on%20Core%20Subset.md)
[(n,t)-Star](Agreement%20Primitives/%28n%2Ct%29-Star.md)
# Commitment / Verification Primitives
---
[Commitment schemes](Commitment%20Primitives/Commitment%20schemes.md) 
[Feldman commitments](Commitment%20scheme%20variations.md#Feldman Commitments)
[Pedersen commitments](Commitment%20scheme%20variations.md#Pedersen commitments)
Polynomial commitments 
# Papers
----
[HoneyBadgerMPC and Asynchromix](Papers/HoneyBadgerMPC%20and%20Asynchromix.md)
[Efficient Asynchronous Verifiable Secret Sharing and Multiparty Computation](Papers/Efficient%20Asynchronous%20Verifiable%20Secret%20Sharing%20and%20Multiparty%20Computation.md)
[Asynchronous Secure Computation](Papers/Asynchronous%20Secure%20Computation.md)
