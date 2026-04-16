# Abstract
---
The system is considered a completely asynchronous network, where two parties are connected via a private channel, and some of the parties may be faulty. A computation, to be considered secure, must take into account the fact that it must be completed even if we never hear from the corrupted parties.

It's also considered that whatever can be securely computed in an asynchronous network, in the presence of a trusted party, can also be computed in a network in which no such party exists. 

The system may have 2 different fault types: [Fail-Stop faults](Fail-Stop Fault.md) and [Byzantine faults](Byzantine fault.md). In the first, the number of faulty parties cannot surpass a third of the total, while, for the system to resist the second, it cannot surpass a fourth of the total parties. The [AVSS](.md) constructed is a [Perfect Asynchronous Verifiable Secret Sharing](.md) scheme contained in the dynamically growing input of the non-faulty parties.

# Introduction
---
Security means preserving the correctness of the outputs as well as the privacy of the inputs, when faults are present. A protocol is considered *t-secure* if when dealing with at most corrupted *t* parties, the non-faulty parties still get the desired output, and the faulty parties learn nothing from the values in the computation, except their own.

When studying an asynchronous system, we must not assume there exists a global clock. We must know that the messages may be arbitrarily delayed, , but eventually received, and that the order of arrival, may be different from the order of departure of the messages.

It's also known that the output of any *n-party* protocol in the presence of *t* potencial [fail-stop faults](Fail-Stop Fault.md) can be based on only *n-t* inputs, since *t* inputs may never join computation.

The ideal scenario is a scenario for secure computation that consists of adding a trusted party to the existing network. Since the party is present, no faults happen, and if we can equal irl computations to this ideal scenario, then said computations are secure. So we must show that whatever can be computed there securely, can be computed in a secure manner when the trusted party is gone.

This paper defines new tools: a [Perfect Asynchronous Verifiable Secret Sharing](.md) for *n >= 4t + 1*, since if we improve it any more, the probability of errors happening may increase; an [agreement protocol](.md) for dynamically growing inputs, whose application is to agree on the set of parties which have sent a specific message to all non-faulty parties; and an on-line error correcting technique.

# Definitions
---
A computation in the asynchronous model is a sequence of steps. In each step a single party is active, and said party is activated by receiving a message. After it performs an internal computation, possibly sending messages to outgoing channels. The order of the steps is controlled by an [computationally unbounded](computationally unbounded.md) [scheduler](scheduler.md). Since the channels are private, we consider it an [oblivious scheduler](oblivious scheduler.md), the only information known is the origin and destination of the message being sent. Knowing the contents would violate the system's security.

Some parties may be faulty, in a [Fail-Stop Fault](Fail-Stop Fault.md) a party may stop sending messages at some time, but we assume that said parties still receive messages and output. The faults considered, however, are only static, that is, the set of faulty parties is determined and fixed at the beginning of the computation, and the attacker does not corrupt parties as they wish as the protocol goes on.

For a computation to be secure it needs to be equivalent to the parties sending their input to a trusted party, said party computing the output and sending it back to the original party. As said before, the parties cannot wait for more than n-t inputs in order to proceed with the computation.

The following scenario is the recommended: bad parties may be able to combine their inputs and alter them, without the good parties knowing. The parties send their inputs to the trusted parties, however the scheduler delivers the messages in an arbitrary manner, arriving only the messages of the parties on a core subset of size at least *n-t*, which is independent of the inputs. Upon receiving it, C computes some predefined [estimation function](estimation function.md) based on C and the inputs of its parties, where we set the inputs of the parties not in C to 0. Next, the trusted party sends the function value and the Core set to all the parties. The good parties will then output what they received from the trusted parties, while the bad output some arbitrary function of information gathered or created during computations.

In order to simplify, we consider that the bad parties and the scheduler are under one coalition and represent one adversarial entity, with unlimited computational power.

Both the output of bad and good parties are computed based on the same value of random input r. For it to be considered secure, we must ensure [termination](termination.md) and [security](security.md).

# Primitives
---
The paper uses 3 primitives:
- ACS - Agreement on a Core Set
- BA - Byzantine Agreement
- Broadcast

The ACS is used for the parties to agree on a common set of at least n-t parties, whose Broadcast has been successfully completed. The good parties' output, in this protocol, is the said common subset. The inputs are variable sets that keep accumulating during execution.

For *n>=3t +1* , ACS has two phases. In the first phase, a part awaits until its dynamic input is of size m, then it performs log<sub>2</sub>n iterations, in which the part sends the current contents to all other parties and collects the sets sent by the other parties. Once it receives n-t sets, the party continues to the next iteration. The intersection of all the good parties inputs is of size at least m. In the second phase, the parties run M BA protocols. In the ith BA, the parties decide whether i is part of the agreed set or not. What happens is that to the ith BA, the party sends 1, if i is in its dynamic input, and 0 if it doesn't. If the common output is 1, then i belongs to the Core set. BA properties ensure that the set contains the intersection of the dynamic inputs of all parties that finished the first phase and that every element in the core set eventually is in the dynamic input of every good party.

Every good party will eventually complete the protocol, provided that the accumulative sets are (m,t) - uniform collections.

# Fail-Stop Faults
---
Its possible to securely t-compute any function with input partitioned among n parties, as long as n>=3t +1, and the faults we are dealing with are [Fail-Stop](Fail-Stop Fault.md). The construction is also resilient against parties which only try to gather information but otherwise follow the protocol. For it to work we assume there is a commonly known field *F* , and that the parties have an arithmetic circuit for computing *f*, with addition and multiplication gates of in-degree 2.

Each party shares their input among other parties with smth akin to [Shamir's Secret Sharing](Shamir's Secret Sharing.md) scheme, namely, the party picks a pi() of degree t, with pi(0) = xi and sends pi(j) to the jth party.

Next, the parties agree, using ACS, on a common set, *C* of parties that have shared their input successfully. Once that's done they proceed to compute *fc(x<sup>-></sup>)*. Firstly the inputs of the parties not in *C* are set to 0 and then the parties evaluate the given circuit gate by gate. For each gate, the parties use their shares of the input lines to jointly and securely generate a random poly of degree t, where Pi compute p(i) and p(0) is the output value of the gate. Once their share is computed, the parties send a message. When a party receives *n-t* of those messages, they invoke a final reconstruction sub-protocol. They reveal their share of the output line and interpolate the value from the received shares.

Evaluating an addition gate is easy, its a linear combination, requires no communication, and each party just sets their output line to be the sum of its shares in the input lines.

Evaluating multiplications is harder, having A() * B() = C(), where a and b are polys associated with the input lines and c is the output line result, the parties must jointly compute a C(), such as C is of degree t and C(0) = A(0) * B(0). To do so, parties generate a poly D of degree 2t and the parties calculate D(0)  = A(0) * B(0), then the parties share the truncation of D, the t+1 coefficients of C are the lower t+1 coefficients of D, and as such, the result of that shared truncation should be C. T shares of D and t shares of C give, nonetheless, no info about C(0).

For randomization, parties first generate a random poly H of degree 2t, where H(0)=0, in order for each Pi to have its own H(i). Each party then, also selects a random Hi, where Hi(0) = 0, and shares it among the other parties, where the jth party gets Hi(j). In a synchronous setting we could set H to be the sum of all Hi, however we cannot wait for them all in an asynchronous setting. Instead, using ACS, parties agree on a set of parties that successfully shared their H.  H() will be set using the Hi of the parties in the set, and now, D() is defined as being A()* B() + H().

Degree reduction makes uses of [matrix](Matrix.md) properties, since its known that exists a fixed nxn matrix such as c = dM. The desired output of each party Pi computes as the linear combination of the outputs of the party as the summation of the term d<sup>-></sup>* M<sub>j,i</sub>. In a synchronous setting that could be calculated just as that, however it requires all the values held by the parties to be shared, and that is not a possibility. To deal with it we once again make use of defining a core set of *n - t* parties, that managed to share their input. Each party then locally computes Ag and then runs *n* reconstruction protocols. The ith protocol computes xg * Agi making the appropriate linear combination. x is a d interpolated vector if there exists a poly p() of degree d where xi = p(i) for every i. All these properties allow us to finally truncate D() to C().

--- ad more stuff

# Byzantine faults
---
The byzantine protocol is almost the same as the previous one but adapted to this kind of faults in the system. This adaptation is done by extending [Shamir's Secret Sharing](Shamir's Secret Sharing.md) to an [AVSS](.md) scheme, and then by modifying the multiplication step. Both extensions make use of the methods for [Generalized Reed-Solomon codes](Generalized Reed-Solomon codes.md).

The [AVSS](.md) scheme is described for 4t + 1. While other schemes achieve a 3t +1, these have a small probability of errors, and as such the [Perfect Asynchronous Verifiable Secret Sharing](.md) was the one chosen.  A [VSS](VSS.md) consists of two sub-protocols. The first is the sharing protocol, in which a dealer shares a secret among other parties, and the second the reconstruction protocol, in which the secret is reconstructed from the shares generated. For it to work it needs to follow some properties:
-  A good dealer should be able to share the secret in a reconstructible way
- If one good party accepts the share, all good parties do so
- A unique secret will be reconstructed, even if the dealer is bad
- If the dealer is good the shared secret must be unknown to the adversary as long as no good party as started the reconstruction protocol
As such they must follow the usual properties of termination, correctness and secrecy. If the dealer is bad, a good party may not have to finish the protocol.

In the first phase a party must receive their secret from the dealer, verify with other parties that their share defines something and then locally compute and output their correct share. In the reconstruction phase, the party sends its share to some parties in a set, and after parties in the set receive enough shares, they reconstruct the secret. Basically, there is a poly p(), such that the output of the party on the sharing phase is p(i) and p(0) is the shared secret. The protocols used in this are the [V-Share](V-Share.md) and the [V-Recon](V-Recon.md).

























#paper #incomplete