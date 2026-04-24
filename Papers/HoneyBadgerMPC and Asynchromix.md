# Abstract
---
[MPCSaaS](MPCSaaS.md) is a current approach to build privacy preserving communication systems. However, current [MPC](MPC.md) implementations are inadequate, Since they do not address [fairness](.md) or [robustness](robustness.md). A single malicious server may be able to crash the system and still see the operation output, which creates vulnerabilities to [censorship](.md) or [de-anonymization](.md) attacks. The plan of the HoneyBadgerMPC is to combine a robust [online phase](MPC.md) with an optimistic [offline phase](MPC%20.md), which runs continuously along side the online one.

# Introduction
---
[Tor](Tor.md) only provides some weak form of anonymity against traffic analysis, having been successfully attacked before using [strong adversaries](strong%20adversaries.md). [Distributed ledgers](Distributed%20ledgers.md) or blockchains require a stronger form of anonymity, and even the use of [zero-knowledge proofs](zero-knowledge%20proofs.md) is undermined unless doe through a protected and encrypted service like [Tor](Tor.md).

## Anonymous Communication from MPC

Using secure multi-party computation is a natural approach for dealing with distributed applications that need strong privacy guarantees. The [MPCSaaS](MPCSaaS.md) setting where servers continuously process encrypted inputs from the clients is becoming more popular

## The need for Robustness in MPC

No current [MPC](MPC.md) implementations guarantees output delivery in the presence of a single active fault. Even worse, they do not guarantee [Fairness](.md) in a sense that an attacker might be able to see an output while stopping the servers from doing so as well.  This can be catastrophic because an adversary may be able to link the messages of clients who retry sending them in a new or restarted instance, basically linking their two private identity's on the system.

## Challenges in providing robust MPC

Vast majority of guaranteed output protocols based on [Shamir's Secret Sharing](Shamir%27s%20Secret%20Sharing.md) are sensitive to assumptions about network synchrony, meaning they rely on synchronous failure detectors to ensure confidentiality and integrity. If a server is temporarily unresponsive, its timed out and ejected from the network, which reduces the servers fault tolerance. IF *t* parties are timed out and ejected from the network, then a single corruption among the remaining servers could compromise the clients confidential inputs.
A [Byzantine fault](Byzantine%20fault.md)'s tolerance of  *t < n/3* is the lower bound even for agreement tasks that do not require any confidentiality

## The approach

[Asynchromix](Asynchromix.md) proceeds in asynchronous epochs, where in each epoch the system selects a subset of k clients and mixes their inputs together before publishing them. The system employs asynchronous broadcast protocols to receive secret shared inputs from untrusted clients and initiate mixing epochs in a robust and distributed way. Its split on the online/offline phase paradigm.

The protocol cost of the offline phase is comparable to that of the online phase, meaning they can run it continuously on the background while the online phase runs, . This later phase is entirely robust, the algorithms used on the offline phase are more efficient but not as robust, for the pre-processing to be fast.  A buffer of values is used to guarantee the online phase always has present inputs and to ensure robustness in presence of faults, this buffer is filled in the efficient non robust phase of the protocol.

## Realizing low latency and robust mixing

For mixing they use 2 approaches, a [switching network](Switching%20network.md) that requires log² k rounds and O(nk log² k) communication shuffles for k client inputs. And [power mixing](PowerMix.md) that has a O(nk) communication overhead but a O(nk + k³) computational cost for each node. This allows for lower latency in mixing, allowing larger mixes for stronger computers as well. It can mix an arbitrarily large number of messages in only 2 rounds. Its also capable of creating arbitrarily many power of a shared secret in one online communication round. This MPC's [primitives](primitives.md) may be used for robust secure computations beyond just secure anonymous broadcast.

# Preliminaries
---
The HoneyBadgerMPC setting involves *n* parties where up to *t < n/3* parties can be compromised by [ Byzantine adversaries](Byzantine%20adversary%20.md), by relying on [Shamir's Secret Sharing](Shamir%27s%20Secret%20Sharing.md).  
To reconstruct a *s* from {{s}} requires [interpolating](interpolating.md) the polynomial from shares received by other parties. However t of these shares may be erroneous. 
In an asynchronous network we cannot distinguish a crash from intentional withholding of data, therefore we can only expect to receive shares from n-t parties in the worst case. In that sense, one optimistically attempts to interpolate after receiving t+1 shares, if the result coincides with the *2t + 1* shares as they are received then we know its correct, otherwise, we have to wait for the *3t + 1* total shares, to correct *t* errors and find a *t-degree* polynomial that can be interpolated and coincides with all shares. The algorithm uses [FFT's](FFT.md) rather than [Vandermonde matrix's](.md) lowering the computational cost from O(n²) to a quasi linear cost of O(n log² n).

**Batch reconstruction** - the idea in [batch reconstruction](.md) is to apply the [Vandermonde matrix](.md) to expand the share secrets into a set of sharings. In the first round each server sends their shares of each value to the respective server and then uses [Robust-Interpolate](Robust-Interpolate.md) to reconstruct a different share. In the second round the servers share this new share and use [Robust-Interpolate](Robust-Interpolate.md) again to recover the values desired. This is done in order to [amortize](.md) the public reconstruction of the batch.

Linear combinations of [SSS](Shamir%27s%20Secret%20Sharing.md)-shared secrets can be computed locally, preserving the degree of secret sharing without any communication between servers. In order to so, using arbitrary arithmetic with [MPC](MPC.md) circuits, we need a way to multiply secrets together. This is where the [Beaver's trick](Beaver%27s%20trick.md) comes in, multiplying two t-sharings using pre-processed [Beaver triples](Beaver%27s%20trick.md) .  
Having {{x}} and {{y}} and wanting to , find out {{xy}} we first need to compute {{a}}, {{b}} and {{ab}} where a and b belong the designated field F. Then, using the following equation, one can extract {{xy}}:

- **{{ab}} = (a-x)(b-y) + (a-x){{y}} + (b-y){{x}} +{{xy}}**

If a and b are truly random, the Open(a-x) and Open(b-y) reveal no info of x and y. For this equation then we must publicly open both of them as well as consume a [Beaver triple](Beaver%27s%20trick.md). Using [BatchRecPub](BatchRecPub.md) as well as the offline phase to fill the buffer with triples we get the required values to be able to do the computations. Doing this process for many multiplication gates at once allows us to process many gates at the same circuit depth.

In order to do the above process we have to have a steady supply of [Beaver triples](Beaver%27s%20trick.md). As the offline phase can be run for an indefinite amount of time, it was relaxed in robustness in order to make it more efficient. First we have to extract randomness, where different servers contribute to make the random secret shares. To produce a t-sharing of random elements of the field *Fp* we apply and [hyperinvertible matrix](.md), a [Vandermonde matrix](.md) in this case, computing thevarious secret shares by:

- **( {{r1}}, ..., {{rn}} ) := M( {{s1}}, ..., {{sn}} )**

Being that each *si* is contributed by the server *Pi*. Using M ensures the result is truly random and the shares have the correct degree. By having the servers reconstruct the columns of the matrix they can verify the degree is indeed right. By being hyperinvertible, we ensure that all inputs are of the correct degree. However, since we require all parties to be online this makes it so its impossible to guarantee output if any server crashes during protocol.

To generate the [triples](Beaver%27s%20trick.md), we make use of random double shares that are *t* and *2t* sharings of the same value.. For that, we make use of [RanDouSha](RanDouSha.md). Each server contributes with a pair of shares, and the first t+1 pairs are used as output by applying *M* and the rest are used as a checksum. We then calculate: 

- **{{ab}}*2t* = {{a}}*t* * {{b}}*t*** 
- **{{ab}}*t* = Open({{ab}}*2t* - {{t}}*2t*) + {{r}}*t***

Performing [degree reduction](degree%20reduction.md). Other than this the offline phase also is used to prepare the random bits and the *k* powers of random elements. By using the mentioned techniques any pre-processing task can be done. The cost of this phase is related to the number of [batch reconstructions](.md) and the number of random shares needed.

The [asynchronous reliable broadcast primitive](asynchronous%20reliable%20broadcast%20primitive.md) is used to receive client inputs. A [RBC](RBC.md) protocol satisfies validity, agreement and totality here as well. The protocol picked is Bracha's with the [Merkle tree](Merkle%20tree.md) and [erasure-code shares](.md), in order to get a broadcast cost of O(n|v| + n²log n). For the [asynchronous common subset primitive](asynchronous%20common%20subset%20primitive.md), they use the [CommonSubset](CommonSubset.md) in order to figure out which instances of the [RBC](RBC.md) have terminated, and initiate the next mixing epoch. Each server begins with a vector, and the mentioned protocol outputs an agreed upon vector of n values that include at least *n - 2t* inputs from correct parties, as well as up to t default values. This also ensures the needed guarantees from the previous primitive, and the vector has the entries that will be used to mix on the next epoch. In the paper they use the instantiation of said protocol from HoneyBadgerBFT, a [BFT](BFT.md) implementation. BEAT0 is an option with more efficient [cryptographic primitives](.md), but both grow n² in small messages and linearly in bigger ones, in the overhead. Others may suffice if there is no need for asynchronous [liveness](liveness.md).

# Robustness
---
MPC's have to guarantee the defense against benign failures like [system churn](system%20churn.md) but also from , [network partitions](network%20partitions.md) or [denial of service](denial%20of%20service.md) attacks. [Robustness](Robustness.md) is highly desired in distributed services because of that, but its barely implemented most times. There are 3 forms of [robustness](Robustness.md) that the services must ensure.  No current implementation aims to ensure [Fairness](Fairness.md) in the service, against an active attacker, being also vulnerable to stoppage of [output delivery](G.O.D..md) or even attacks against the [liveness](liveness.md). hbMPC tries to achieve a security of *t < n/3* while ensuring [fairness](fairness.md), [guaranteed output](G.O.D..md) online but not offline and asynchronous safety and [liveness](liveness.md). To ensure these, or try to, usually other protocols rely on restarting the whole computation with new or the same members, while eliminating the corrupted ones. This allows them to guarantee security in *t < n/3* or even *t < n/2* using [cryptography](cryptography.md). Using virtualized protocols is also an option but ti relies on being able to time out nodes that are unresponsive and restart the computation with the rest. This however decreases the fault resistance, as there are less resilience, meaning confidentiality can be, more easily, compromised. 

There is, therefore, a trade off between [liveness](liveness.md) and the safety properties, the more we tolerate for [liveness](liveness.md), the less we can tolerate for safety. P.e. setting the timeout parameter low enough to only tolerate benign crashes in order to be more efficient, might mean even shorter duration of network partitions that weakens the security threshold among the remaining nodes.

The protocol is considered to have asynchronous safety if these properties withstand while working asynchronously, up to t corrupt parties. Usually purely asynchronous protocols guarantee some of these properties without assuming bounded synchrony and broadcast channels. For this to work it is necessary to have a guarantee of *t < n/3*, making the lower bound for it to work.

Communication overhead is also a critical factor in how well the network will scale. The main focus is on [amortizing](.md) the overhead to deal with large batches of operations. MPC should have a linear communication overhead, growing with n. This means that the needed bandwidth for each node remains constant even when new nodes are added. Computation overhead might be a concern as well.

Dealing with [intersection attacks](.md) is one of the main reasons to not use unfair implementations for AsynchroMix. The focus is also avoiding have to provide any compensation at all due to unfairness,  by simply not allowing it to happen. This means no synchrony based safety protocol can be used.

By running the offline phase ahead of time its guaranteed a constant presence of [triples](Beaver%27s%20trick.md) on the buffer used to run the offline phase, allowing a partially asynchronous protocol to be used. This way, we can ensure liveness offline and online. In order to reduce the cryptographic overhead, the resilience guarantees are relaxed as well as the ones pertaining to [guarantee of delivery](G.O.D..md).

# Overview
---
AsynchroMix is an application of the [MPCSaaS](MPCSaaS.md) approach to the anonymous broadcast communications, where clients send confidential info to the server nodes, which mix messages before making them public. Since robustness is a focus, the system must not rely on timeouts or time-bound parameters to be configured correctly. The network is assumed to be under adversary's control, one that may arbitrarily delay messages, duplicate them or even deliver them out of order. We must assume that the adversary cannot drop messages between two parties. The threat model also includes a fraction of the servers being maliciously corrupted

The system is a set of clients that asynchornously communicate with servers that are asynchornous between themselves as well using messages of a fixed bit size. Its assumed at most *t < n/3* servers are [Byzantine corrupted](.md).  It proceeds in sequential mixing epochs, where in each a certain ammount of messages are mixed. This protocol guarantees [anonymity (safety)](anonymity%20%28safety%29.md) and [availability (liveness)](availability%20%28liveness%29.md). HoneyBadgerMPC performs the secure computations making use of the asynchronous and maliciously-secure primitives. Each epoch is a standard robust online phase, using the means mentioned, [triples](Beaver%27s%20trick.md) and [batch reconstruction](.md), and the offline phase is an optimistic phase that tries to fill the buffer with needed precomputed values.

Clients are considered untrusted, because of this we need to receive [shared secrets](.md) while guaranteeing that the inputs are valid, consistent and available for all nodes. It should be used [Asynchronous Verifiable Secret Sharing](Asynchronous%20Verifiable%20Secret%20Sharing.md) , but that would increase the overhead, due to increased communications.  Instead its used a pre-processing approach where m is blinded with a random value, a random share {{r}}, generated in the offline phase and reconstructed to C, that is, each server sends to each client a share like that. The client then blinds the message:

- **m' = m + r**

Broadcasting it after. Server then compute the share by doing:

- **{{m}} = m' - {{r}}**

The broad cast is done using [Asynchronous Reliable Broadcast](Asynchronous%20Reliable%20Broadcast.md), guaranteeing that if any server receives the m, all servers receive m.

Each mixing epoch begins when the servers have received inputs from enough clients, reaching an agreement on a subset of *k* client inputs. This is done using [CommonSubset](CommonSubset.md) where each server inputs its view of which client inputs are available for mixing. For honest servers this is the set of inputs received from [Asynchronous Reliable Broadcast](Asynchronous%20Reliable%20Broadcast.md), and after running the code they will obtain the desired set.

The mixing proceeds as the online phase of MPC, running either [power mix](PowerMix%20.md) or [iterated-butterfly](Iterated-butterfly.md), and requiring the [Beaver triples](Beaver%27s%20trick.md) and a [batch reconstruction](.md) of *t-sharings* that ensure the desired *t < n/3* security through [Reed Solomon Decoding](Reed%20Solomon%20Decoding.md).

Since its continuously running the offline phase can be run alongside it. Latency while not critical is a worry since it must still keep up with the demand. This offline phase is based on decoding *2t-sharings* and therefore makes progress only when at least *n* nodes are responsive. Its nonetheless considered ok for it to not be robust and in place, be faster.  However, if nodes fail, there will eventually be a shortage of [triples](Beaver%27s%20trick.md) and the clients will have to move to a new instance of the service.

Anonymity is guaranteed by having each epoch only mix if there are inputs from *k* different clients, and using pre-processed random shares ensures that the secret shared inputs depend only on the broadcast values from clients. Thanks to the [perfect symmetry](perfect%20symmetry.md) in its equation format the [canonical ordering](Canonical%20ordering.md) of the outputs depends only on their values as well and not on their permutation order. The [Switching network](Switching%20network.md) induces a random permutation which is nearly uniform. For availability, each clients input must be eventually mixed and each epoch must be robust. For the first need, once a broadcast is received by every honest server, then the corresponding bits in the next epoch will be set by all honest servers, ensuring that the message is included in the mixing epoch that follows. For the second, notice that if at least one server has received the broadcast then, through the agreement property of [Reliable Broadcast](Reliable%20Broadcast.md) this input will be available to every honest server. This means that [Asynchromix](Asynchromix.md) guarantees all the needs.

[DC-Net](DC-Net.md) strategies usually end up not being the best approach due to any participating node being able to abort the execution and force it to happen again. Relying on timing out or other synchronous assumptions is a great mistake. Compromised nodes may attempt to degrade performance by stalling until being timed out. Reducing timeouts time makes it so more honest nodes get booted, degrading security. Deanonymization through [interference attacks](.md) is a possibility. However these schemes can maintain anonymity against much larger collusion among nodes. The mixing done in this [MPC](MPC.md) other than the [switching networks](Switching%20network.md) works by associating a random tag to each message and obliviously sorting the tags using [MPC comparison](MPC%20comparison.md) operators.

# MPC Mixing
---
There are two approaches used: a [butterfly switching network](Butterfly%20Network.md) that yields an almost ideal random permutation, and [PowerMix](PowerMix.md), that uses [Newtons Sums](Newtons%20Sums.md) to do the mixing.
The first method uses a secret-shared random bit from the offline phase and a single [multiplication](MPC%20.md), requiring *O(log² k)* rounds, plus a communication cost and a server computation cost of *O(n log² k)* each. The second usually requires *O(log k)* communication rounds but using computation trade-offs it only takes two. This alternative to the [Switching network](Switching%20network.md) mixes a batch of *k* messages, by computing the powers of each message, *m*, from 1 to *k*. Then the sum of each power is computed, that is, the sum of all messages to the power of 5, p.e., and used to reconstruct a value *s*. Finally the sum method is used as a solver for the set of m. Using pre-computed powers of a random value from 1 to *k*, the number of rounds is greatly reduced becoming a constant value.

While common to use,  [switching networks](Switching%20network.md) can be biased in the permutations they perform each layer, some being more likely than others. However, *O(log k)* iterations of a random [butterfly network](Switching%20network.md), that consists of *O(log k)* layers is determined to be adequate shuffling. The round complexity is therefore, as mentioned before, *O(log² n)* and the overall communication cost *O(k log² kn)*, due to the *k* multiplications needed in each layer. The computational cost is also *O(k log² kn)* due to the needed total multiplications. In this [MPC](MPC.md) implementation its called [Switch](Switch.md). 

[PowerMix](PowerMix.md) attempts to reduce communication at the cost of computation. First they calculate the sets *k* powers of the secret message m. Its possible to do in O(1) communication per shared secret. The second step is to use [Newtons Identity](Newtons%20Identity.md)'s to solve a system of equations of the form S3 = m1³ + m2³ + mk³, with i = 3. Servers can only obtain Si by computing {{Si}} locally and then publicly reconstructing. The another set of equations is solved in order to obtain m'i in [canonical ordering](Canonical%20ordering.md).

For each secret share from the clients, the set of powers is computed. This can be done using [Beaver triples](Beaver%27s%20trick.md) *k-1* times or using the constant round [unbounded fan-in multiplication](unbounded%20fan-in%20multiplication.md), which adds a *3x* factor of extra work. Either way, we reconstruct *O(k)* elements. instead pre-processing is used to compute all powers, by publicly reconstructing only a single element, using the pre-computed powers of a random element that we got from the offline phase.

- **m^k - r^k = (m-r) \* (Σ m^(k-1-l) * r^l)** (0<=l<=k-1)

Which by making C = (m-r) and using the secret share brackets, we get an expression for any term {{m^i\* r^j}} as asum of monomials of small degree.

- **{{m^i\* r^j}} = {{r^(i+j)}} + C \* (Σ {{m^(i-1-l) \* r^(j-l)}})** (0<=l<=i-1)

Using [ComputePowers](ComputePowers.md) we get all the powers of the message *m* by [memoization](memoization.md). The algorithm requires a total of *k²/2* multiplications an *k²* field additions, and the memory table needed can be reduced to *O(k)*. since for {m^i\* r^j}} we only need monomials of degree i + j -1, so we can lose lower degree terms.

After its required the reconstruction of shuffled valued from the power sums, that is, we must be able to extract *mi* from *Si*. Assuming we are mixing *k* messages to compute the appropriate powers.. Then all servers calculate locally,*Sj*, from the sum of all messages to the power of *j*, and publicly reconstruct *Sj*. By defining *f(x)* as a polynomial where *f(x) = 0* has roots in *m1*, *m2*, ... , we are able to calculate the values *ai*, the coefficients of the polynomial. We then, can extract the k roots of *f(x) = 0* with O(k²) computations. After, we recover all *mi* and get our final mixing set that consists of the found k messages.

The offline phase must be efficient enough at generating the pre-requisites needed for the computations, both for converting inputs into secret sharings and for realizing the mixing programs. Handling inputs is the easiest, as it only needs to generate a t-shared random value for each. The randomness extraction is done through [RanDouSha](RanDouSha.md) but with one matrix operation with half the number of inputs and outputs, that is, simply half a call to the function. There is additional pre-processing needed for the mixing programs. The first needs random selector bits and [Beaver triples](Beaver%27s%20trick.md), and the second needs *k* secret-shared powers of the same random value, this is done through calls to [RanDouSha](RanDouSha.md) and [BatchRecPub](BatchRecPub.md). The [Switching network](Switching%20network.md) use *1.75k log² k* of the first and *k log² k* of the second, and the [PowerMix](PowerMix.md) uses *k²* of both.

The system can easily be adapted for messages of larger but fixed size messages of multiple fields each, by either using pre-processed selection bits, where the first element of message i is permuted the same as the second element, or by using a tag that would be used to link parts of messages together, which wouldn't collide except with a probability of (1/2^k).

# Implementation 
---
Implementation was done in Python with modules in C++. Benchmarking was done to find out that the main cost in the online phase is the [batch reconstruction](.md) when doing the [Switching network](Switching%20network.md). Two variants were used, one based on matrix multiplication and the other based on [FFT](FFT.md), which proved to be better for networks larger than *n = 2048*. [NTL switches](NTL%20switches.md) uses the matrix at 70, which meant that FFT evaluations were between when using a hybrid approach compared to just using [Vandermonde matrix's](.md) for interpolation. The sweet spot was using [FFT](FFT.md) for evaluating the values but [Vandermonde matrix](.md) for the interpolation of values.

The performance of the system was not dependent on the cost of error correction, since its only done once per faulty party. Once one is identified all shares from said party are discarded, and the interpolation resumes without using it. In an extreme case where 33 servers fail, the delay would be at most 33s.

Its expected that the per server per share cost reaches a constant for values large enough. As n grows larger since there is an additive overhead quadratic in n means that larger values of k are needed for the [amortizing](.md) to have effect.

There is room to improvement due to serialization and the usage of python and [PowerMix](PowerMix.md) is a better choice for networks with many k's or on networks with more computing power and less bandwidth.

# Explanation (how online phase and preparation works)
---
The online phase is an entirely [robust](Robustness.md) phase where a secure mixing of messages occur. It has [G.O.D.](G.O.D..md) and ensures [fairness](Fairness.md) in a malicious setting without depending on synchronous assumptions.

IT resists up to *t< n/3* corrupted servers corrupted by a global adversary, which can't stop honest servers or parties from getting the outputs they need.

The online phase depends on an asynchronous and usually online process where many pre-computations happen as well as where the messages are sent to the severs and the messages to be mixed are chosen.

The pre-computations like the [Beaver triples](Beaver%27s%20trick.md) and the random shares are done by the servers in preparation for the online phase.

Both mixing protocols require a secretly shared value *JrK*  for each client's input in order to mask the message.

The [Iterated-butterfly](Iterated-butterfly.md) requires the [triples](Beaver%27s%20trick.md) for the secret multiplication in each switch. As well as a randomness bit *JbK*  that is secretly shared and used to decide if the pair of inputs is swapped around or not.

The [PowerMix](PowerMix.md) requires the k-powers to mix a batch of k messages. The powers must be secretly shared through a random value.

The first step in the process is the input and masking of the message from the Client. The client does so by using the random value rj and obtains m'j, the mask, by:

- **m'j = mj + rj**

rj is obtained by the client, Cj, not computed. To do so, rj is initially generated as a random secret sharing done by the servers, where each gets their *JrK*. Each server sends to Cj their share of the secret. After receiving their shares, it uses [Robust-Interpolate](Robust-Interpolate.md) to secretly recover the value rj. After that, m'j is sent, for all servers using [Reliable Broadcast](Reliable%20Broadcast.md), and when receiving the servers compute their own share of the message, *JmK* by using the random share *JrK*.

- **JmK = m' - JrK**

This allows us to avoid using [Asynchronous Verifiable Secret Sharing](Asynchronous%20Verifiable%20Secret%20Sharing.md) and saving on communication complexity or even in some computation complexity. Server announces it received the input from client j going to its index and adding  +1 indicate its present. When servers are deciding if they processed Cj message yet they check *done_j* and *input_j* and if the last is bigger, than they have messages to process. When they process Cj message, *done_j* increases by one.

Then the servers must agree on which *k* inputs to process. To do so they use [CommonSubset](CommonSubset.md). Each server will create a vector with what inputs pass the check previously mentioned. After using the protocol the output is again a vector with the common *k* messages to be mixed. This protocol is asynchronous.

Next comes the mixing phase where the messages are all mixed together. To do so, as above mentioned, the protocol can pick one of two options, the messages are mixed, doing a strong cryptographic permutation. The obtained result is a set of messages permuted secretly. 

After that said result is made public, still ensuring anonymity. When a client *Cj* sees their message broadcast the protocol is done and the servers update *done_j* as required.

To pick the mixing protocol used is a bit interchangeable and solely depends on which one was picked or on the conditions of the network. When doing mixes with large *k* it is preferable to use the  [Switching network](Switching%20network.md), otherwise, the [PowerMix](PowerMix.md) should be used, specially if we prioritize low latency and a reduction on communication, its better for bandwidth restricted networks or when servers are more powerful, since it reduces it to 2 communication rounds but demands more calculations.

The mixing and permutation is done in order to ensure anonymity, stopping any attacker from associating the input order to the broadcast order since the latter is almost independent from the first. MPC is used to ensure the security properties defined even in a malicious and asynchronous environment.







 




#paper


