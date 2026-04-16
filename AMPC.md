
# Network Model
---
[Asynchronous Network](Asynchronous%20Network.md)

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
[MPCSaaS](MPCSaaS.md)

# AVSS
---
[Polynomial Generation](Polynomial Generation.md)
[Commitment schemes](Commitment schemes.md)
[Private Authenticated Channel](Private Authenticated Channel.md)
[On-line Error Correction](On-line Error Correction.md)
[Asynchronous Reliable Broadcast](Asynchronous Reliable Broadcast.md)
[(n,t)-Star]((n,t)-Star.md)

# Input Decision
---
[Agreement on Core Subset](Agreement on Core Subset.md)
[Asynchronous Byzantine Agreement](Asynchronous Byzantine Agreement.md)
[Asynchronous Multivalue Byzantine Agreement](Asynchronous Multivalue Byzantine Agreement.md)

# Circuit Evaluation
----
[Degree Reduction](Degree Reduction.md)
[Linear Operations](Linear Operations.md)
[Non-linear Operations](Non-linear Operations.md)

# Core Algebraic / Coding Primitives
---
[Polynomial Interpolation](Polynomial Interpolation.md)
[Reed Solomon](Reed Solomon.md)
[On-line Error Correction](On-line Error Correction.md)

# Communication and Broadcast Primitives
---
[Private Authenticated Channel](Private Authenticated Channel.md)
[Consistent Broadcast](Consistent Broadcast.md)
[Reliable Broadcast](Reliable Broadcast.md)
[Asynchronous Reliable Broadcast](Asynchronous Reliable Broadcast.md)

# Agreement / Asynchronous Consensus Primitives
---
[Byzantine Agreement](Byzantine Agreement.md)
[Asynchronous Byzantine Agreement](Asynchronous Byzantine Agreement.md)
[Agreement on Core Subset](Agreement on Core Subset.md)

# Commitment / Verification Primitives
---
[Commitment schemes](Commitment schemes.md) 
[Feldman commitments](Commitment scheme variations#Feldman Commitments.md)
[Pedersen commitments](Commitment scheme variations#Pedersen commitments.md)
[(n,t)-Star]((n,t)-Star.md)
Polynomial commitments 
# Papers
----
[Long Live The Honey Badger Robust Asynchronous DPSS and its Applications](Long Live The Honey Badger Robust Asynchronous DPSS and its Applications.md)
[Efficient Asynchronous Verifiable Secret Sharing and Multiparty Computation](Efficient Asynchronous Verifiable Secret Sharing and Multiparty Computation.md)
[Asynchronous Secure Computation](Asynchronous Secure Computation.md)
