The standard AMPC protocol can be divided in 3 phases.
# Network Model
---
The usual network model assumed is as follows: [Asynchronous Network](Network/Asynchronous%20Network.md).

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
In the distributed networks case, the machines that run the protocol might be too slow. It might be of interest to offset the computations to a set of various server like machines, without ever having a majority of these servers being controlled by a single entity, in order to preserve the confidentiality and security of the protocol. The [MPCSaaS](MPCaaS/MPCSaaS.md) paradigm is the result of said necessity, making the distributed machines only act as clients to a distributed set of servers that do all the computations.

# AVSS
---
Asynchronous Verifiable Secret Sharing (AVSS) is a really important building block for designing AMPC protocols that tolerate a defined number of adversaries *At*. 
AVSS works as a pair of protocols, corresponding to its two phases, Share and Reconstruction. A dealer *D* decides on a private input *s*, the secret, that will be shared to the n participants of the network
As proposed initially by R. Canetti, in order to be used later on in the verification, the dealer should use [Polynomial Interpolation](Encoding-Decoding%20Primitives/Polynomial%20Interpolation.md) in order to generate a bivariate polynomial h(x,y) where f(x,i) and g(i,y) (for the party Pi) both have degree t. A. Patra further explored this topic, allowing for the polynomial to be (d,t)-shared, where t<=d<=2t, which allows for a laxer need degree reduction during the computation phase. 
After correctly generating the polynomial, the dealer must share the appropriate content to the right party. To do so it makes use of the [Private Authenticated Channel](Communication%20Primitives/Private%20Authenticated%20Channel.md) established between itself and the party Pi as nodes join the network. 
To each party Pi, the dealer sends, in a perfect AVSS with **4t +1** threshold, the row and column polynomials, fi(x) = f(x,i) and gi(y), which will be used later for verification. In the initial paper proposed by Canetti, the later trading of messages and exchange of pairs between Pi and Pj where they verify that fi(j) = gj(i) was enough for verification. Later along the line, with the work of Patra and of Y. Su, cryptographic layers were added, making use of [Commitment schemes](Commitment%20Primitives/Commitment%20schemes.md) , like KZG and Pedersen, to allow for faster verification of the commitments and bigger adversary tolerance, while also allowing the usage of zero knowledge proofs.
The pair verification, after the commitment validation, results in a [Asynchronous Reliable Broadcast](Communication%20Primitives/Asynchronous%20Reliable%20Broadcast.md) of the message "OK" pertaining to the parties involved, Pi and Pj. Each party collects these messages in a graph like structure and employs the usage of the [(n,t)-Star](Agreement%20Primitives/%28n%2Ct%29-Star.md). Once the star is found, it means that the honest parties can establish a CORE of enough honest or well behaved parties. This means that the secret was correctly shared and that enough parties can continue and conclude the protocol. 
Once the party reaches this CORE, it will [broadcast](Communication%20Primitives/Asynchronous%20Reliable%20Broadcast.md) said group, and start the process of reconstructing their share of the secret, h(0,i). In the original model, only parties outside of the star must rely on [On-line Error Correction](Encoding-Decoding%20Primitives/On-line%20Error%20Correction.md) to reconstruct their share of the secret. In order to give it more robustness against malicious dealers, in the more recent versions the parties in the agreed CORE will instead give their sub-shares to all non CORE members in a way to allow for nodes outside of the subset to reconstruct their share of h(0,0).
After this, in the reconstruction phase, [OEC](Encoding-Decoding%20Primitives/On-line%20Error%20Correction.md) will be used once again to reconstruct the final secret in order to complete the AVSS protocol.

*Su, Y. et al. "Dumbo-MPC: Efficient Fully Asynchronous MPC with Optimal Resilience"*
*Patra, A. et al. "Efficient Asynchronous Verifiable Secret Sharing and Multiparty Computation"*
*Canetti, R. et al. "Asynchronous Secure Computation"*
# Input Decision
---
On the second phase of the AMPC protocol, it becomes necessary to pick whose inputs will be used on the computation phase. To do so, [Agreement on Core Subset](Agreement%20Primitives/Agreement%20on%20Core%20Subset.md) is employed. The result of this protocol is the set of participating nodes, whose inputs will be considered for the computation
# Circuit Evaluation
----
Part of the computation phase, these are the necessary parts for the parties to obtain the result of the circuit.
[Degree Reduction](Degree%20Reduction.md)
[Linear Operations](Linear%20Operations.md)
[Non-linear Operations](Non-linear%20Operations.md)

# Core Algebraic / Coding Primitives
---
Primitives that ensure that one of the most important blocks of AMPC work. They are the basis for secret sharing and ensuring the successful reconstruction of the secret later on.
[Polynomial Interpolation](Encoding-Decoding%20Primitives/Polynomial%20Interpolation.md)
[Reed Solomon](Encoding-Decoding%20Primitives/Reed%20Solomon.md)
[On-line Error Correction](Encoding-Decoding%20Primitives/On-line%20Error%20Correction.md) (BerlekampWelch)

# Communication and Broadcast Primitives
---
Primitives used on communication between nodes, from direct communication to the main variations of the broadcast protocol. The objective is to ensure that a set of nodes, usually the honest ones, or even all nodes receive the same information or at least the information the sender wants them to receive.
[Private Authenticated Channel](Communication%20Primitives/Private%20Authenticated%20Channel.md)
[Consistent Broadcast](Communication%20Primitives/Consistent%20Broadcast.md)
[Reliable Broadcast](Communication%20Primitives/Reliable%20Broadcast.md)
Provable Broadcast
[Asynchronous Reliable Broadcast](Communication%20Primitives/Asynchronous%20Reliable%20Broadcast.md)

# Agreement / Asynchronous Consensus Primitives
---
Primitives used to agree on values on an asynchronous or byzantine network. These ensure that when terminated, there is a set of nodes, usually the honest nodes, that all agree on the value (can be multiple) of a certain variable.
[Byzantine Agreement](Agreement%20Primitives/Byzantine%20Agreement.md)
[Asynchronous Byzantine Agreement](Agreement%20Primitives/Asynchronous%20Byzantine%20Agreement.md)
[Asynchronous Multivalue Byzantine Agreement](Agreement%20Primitives/Asynchronous%20Multivalue%20Byzantine%20Agreement.md)
[Agreement on Core Subset](Agreement%20Primitives/Agreement%20on%20Core%20Subset.md)
[(n,t)-Star](Agreement%20Primitives/%28n%2Ct%29-Star.md)
# Commitment / Verification Primitives
---
Primitives that ensure that what was sent corresponds to what is received. Usually is employed in AVSS to make sure that the shares given received from the dealer are in fact the ones they claim to have sent.
[Commitment schemes](Commitment%20Primitives/Commitment%20schemes.md) 
[Feldman commitments](Commitment%20Primitives/Commitment%20scheme%20variations.md#Feldman%20Commitments)
[Pedersen commitments](Commitment%20Primitives/Commitment%20scheme%20variations.md#Pedersen%20commitments)
Polynomial commitments 
# Papers
----
[HoneyBadgerMPC and Asynchromix](Papers/HoneyBadgerMPC%20and%20Asynchromix.md)
[Efficient Asynchronous Verifiable Secret Sharing and Multiparty Computation](Papers/Efficient%20Asynchronous%20Verifiable%20Secret%20Sharing%20and%20Multiparty%20Computation.md)
[Asynchronous Secure Computation](Papers/Asynchronous%20Secure%20Computation.md)
