# Abstract
---
MPC guarantees information-theoretic security for n parties to compute a function over a finite field, as long as a max of t parties are under control of an adversary. AMPC extends MPC to an asynchronous environment. Perfect AMPC is possible for **t<n/4** and statistical AMPC allows for **t<n/3**. 

On the paper both protocols have **n= 4t +1** parties. They make AVSS protocols where one is statistically-secure and has non-optimal resilience,
while the  other is perfectly-secure and has optimal resilience. The scheme is different because it can share a secret with a degree of at most d, where **t<=d<=2t**, which simplifies the degree-reduction step for evaluation of mult. gates on the AMPC protocol

They also design a statistical AMPC with amortized communication **O(n²)** field elements. Its non-optimal resilient but improves the communication complexity.

They present a perfect AMPC with the same amortized communication, and optimal resilience. Being efficient and optimal-resilient.

# 1. Introduction
---
Threshold MPC allows a set of n mutually distrusting parties to securely compute an agreed function **F**, over a finite field *F* even if **t** out of the **n** parties are under control of an active adversary **A** who behaves arbitrarily during execution of the protocol. This **F** is expressed as an arithmetic circuit over *F* that consists of: input, linear (add), non-liner (mult.) random and output gates over *F*. The multiplication gates are the more costly communication wise, among the parties, and therefore, are usually the focus when measuring communication complexity, that is, the number of bits shared by the parties.

MPC usually assumes a synchronous network, with a global clock and a bounded delay, however this doesn't model real-world networks properly, where messages may be delayed arbitrarily but always arrive. This in inherently difficult, since we cannot distinguish a slow but honest party, with delayed messages, from a corrupted party, which sends nothing at all. This implies a party can't wait for the arrival of messages from all other **n** parties, and we must ignore at least t messages, that may be lost or corrupted. We must provide information-theoretic security, against a computationally unbounded **A**.

Perfectly-Secure AMPC or Perfect AMPC do not involve any error in the computations. And its possible only for **t < n/4**. A optimally-resilient perfectly-Secure AMPC protocol requires a design that works with **n = 4t +1** parties. Statistically-Secure AMPC or Statistical AMPC allow for a negligible error, specified by an error parameter, in the computation.  And works with a greater bound of **t < n/3**. So, if designed to work with **n = 3t +1** parties is called an optimally-resilient statistically-secure AMPC protocol. The last one requires higher communication but, in turn, its easier to design. This is due to the fact that they have to tolerate more corruptions and due to the difficulty in designing a statistical AVSS, a major building block of AMPC with the same communication complexity of a perfect one.

To obtain a communication efficient statistical AMPC, we usually have to trade resilience for efficiency. So, sometimes, in other to get a lower communication overhead, we end up lowering the threshold to **n = 4t +1** even on a statistical AMPC, toleration a smaller number of corruptions. There are also a lot of protocols the assume a “synchronization point”, where the network stops behaving as asynchronous, but that model does not correctly reflect the real-world network behaviours.

## 1.1. Our Contributions for AMPC
---

In the paper they present 2 AMPC protocols that work for **n = 4t +1** parties. Both the statistical and the perfect have a communication complexity of **O(n² log |*F*|)** bits, meaning they use the trade-off mentioned in the statistical one, and therefore don't achieve optimal resilience. The statistical works without using the player-elimination framework. For the AMPC they also design two AVSS protocols, one statistical and another perfect, bound by the same thresholds and classifications as the respective AMPC, but with some common properties not achieved before. They use different techniques but also have the same communication complexity.

## 1.2. Verifiable Secret Sharing
---

A VSS scheme has two phases the sharing phase and the construction phase, implemented by the pair of protocols **(Sh, Rec)**. **Sh**, the sharing phase, allows a special party, the dealer, to share a secret **s** among n parties in a way that they can later reconstruct it uniquely during the **Rec** execution, in the recovery phase. If **D** is honest, then the secrecy of **s** is preserved until the end of **Sh**. VSS schemes however assume that the delay of every message is a constant, which is very strong, since a single message delayed a bit more breaks the system. This requires AVSS, the adaptation of VSS to an asynchronous setting.

AVSS work as a threshold structure, the shares makes it somewhat like **t** or less parties can never get info on the secret while **t +1**  or more, working together are enough to reconstruct **s**. The scheme should also work linearly, the shares are linear functions of the secret and the randomness to it associated. Information. Theoretically secure AVSS also fall into 2 categories, the perfect and statistical, that work with the same thresholds as AMPC. The most common approach is to base them on polynomial interpolation, since its linear and allows the sharing of secrets through polynomials.

AVSS schemes are an important building block for  AMPC protocols that tolerate **A**. The sharing phase forces **D** to *t*-share a value, even if corrupted, this implies the valued is shared using a polynomial **f(x)** of degree **t**, such that **f(0) = value** and every honest party **Pi** has access to **Sh = f(i)**. This is denoted as `[v]d`. AVSS are used for 2 things on AMPC, to make parties commit and share their inputs and then to generate several random values, which satisfy some conditions, which are used to evaluate the mult. gate. 

The approach usually involves every party **Pi** *t*-sharing its input xi. Then the parties agree on a common subset of **n-t** parties, such that **xi** has been generated and shared for every party in **C**. Not all inputs can be considered due to the asynchronous nature of the network. The input **xi** remains secure, since the adversary never obtains the **t+1** shares needed to reconstruct it. Once **C** is decided, **F** is performed gate by gate in a shared fashion following the classical approach of Ben-OR et al. Parties interact according to the protocol to generate a *t*-sharing of the output of each gate from the *t*-sharing of the inputs of the gate. Once the final output in generated, the parties reveal their shares and an error correction mechanism is applied to identify the corrupted shares and robustly reconstruct the final output values. This reconstruction is guaranteed since AMPC requires at least **3t+1** parties, the threshold to guarantee a robust reconstruction of a *t*-shared value. The intermediate values preserve the secrecy of **xi** as well as all values used unless shared and reconstructed mid protocol.

The evaluation of the circuit is done locally when dealing with linear gates, since it requires no interaction, due to *t*-sharing being linear. Multiplication gates, however, require communication, they can't be evaluated locally. More specifically,
given `[c]t` and `[d]t` , a *t*-sharing of **e = c + d** can be locally generated as `[e]t = [c]t +[d]t`.
However, the multiplication gates cannot be evaluated locally, as `[c]t · [d]t = [e]2t` , instead of `[e]t` . If `[e]2t` is not converted to `[e]t` then further multiplication of e with another *t*-shared value will raise the degree of the sharing, which makes it impossible to robustly reconstruct the value. This makes it the major bottleneck in the shared evaluation of a circuit. To get `[e]t` from `[c]t` and `[d]t` in **e = c * d** the parties have to interact with each other. The amount of interaction varies and depends on the method used to reduce the degree of the sharing e. That's why the communication complexity is usually considered as the value of the evaluation of a mult. gate.

The most common method to get `[e]t` is to use Beaver's cirucit randomization method. The gates are evaluated using precomputed and *t*-shared random multiplication triples, unknown to **A**. Another approach is to use a single pre-computed *(t,2t)*-sharing of random values, unknown to the adversary. A *(t, 2t)*-sharing of a value r contained in *F* consists of a *t*-sharing and a *2t*-sharing of **r** via independent polynomials of degree **t** and **2t**, making `[r]t` and `[r2]t` both available to the party. This way, parties can get `e[t]` from `d[t]` and `c[t]` by: generating `[e]2t = [c]t * [d]t` and `[a]2t = [e]2t - [r]2t`. After, **a** is reconstructed, and once publicly known, the party does `[e]t = [a]t + [r]t` obtaining the desired value. Its important to know that the parties consider a default *t*-sharing of **a** using the constant polynomial of degree 0. The secrecy of **e**, **c** and **d** is not compromised, since **r** is a random unknown value.

The problem boils down to efficiently generating the *(t,2t)*-sharing or the *t*-sharing of random multiplication triples. The cost for both in the gate is nearly the same. The triple requires *2t*-shared values to be reconstructed, while the other requires one *2t*-shared value. However, the triple is robust at **n=3t +1**, while the later approach requires **n=4t + 1**. To generate the *(t, 2t)*-sharing we can use an existing AVSS scheme, therefore to get all the values we must invoke it  **3t +1** times. We can bring that value down by a factor of **n**, by noting that a *(t,2t)*-sharing can be done from a single *t*-sharing and a single *(2t -1)*-sharing and by introducing an AVSS scheme that can produce a *d*-sharing, where **t<=d<=2t**. This plus having a good communication complexity makes it so the acceleration of the *(2t -1)*-sharing  is possible.

The paper presents 2 AVSS schemes for **n = 4t +1**, one statistical and one perfect. The sharing allows the dealer to *d*-share a value **v**, where **t<=d<=2t**. At the end of the sharing phase there exists a polynomial, **f(x)**, of degree d such that **f(0) = v** and every **Pi** has **Shi = f(i)** of **v**.They also allow the *d*-sharing of several values at once, such that each is considered *d*-shared. This makes it easier to broadcast multiple **l** values at once, making the broadcast independent of the number of values shared, **l**. This is important because the broadcast primitive is very expensive. 

They can generate *d*-sharing of **l** values, with communication complexity **O(ln2 log |*F*|)** bits.and hare **l(d + 1 − t)** values in the sense of “packed secret-sharing” where **l ≥ 1** and **t ≤ d ≤ 2t**, with communication complexity **O(ln2 log |*F*|)** bits.

The first allows for a better communication complexity when generating *(t,2t)*-sharing of random values. The second implies that hat the amortized cost of sharing a single value tolerating an active adversary is O(n) field elements, which matches the complexity of sharing a single value tolerating a passive adversary.

### Efficient generation of *(t, 2t)*-sharing of random values: 
---

The objective is to generate a *(t, 2t)*-sharing*of a random value efficiently, since such sharings are useful in AMPC to simplify multiplication while preserving security. A **(t, 2t)-sharing** means that the same secret is shared once using a polynomial of degree **t** and once using a polynomial of degree **2t**, with both sharings consistent on the constant term.

The earlier method starts by producing **3t + 1 independent random values**, namely **r(0), r(1), …, r(3t)**, each of which is **t-shared** using AVSS. From these values, two polynomials are constructed. The first polynomial **P(x) = r(0) + r(1)x + ··· + r(t)x^t** has degree at most **t**. The second polynomial **Q(x) = r(0) + r(t+1)x + ··· + r(3t)x^{2t}** has degree at most **2t**. Since both polynomials evaluate to the same constant term at **x = 0**, namely **r(0)**, the shares defined by **P(x)** form a **t-sharing** of **r(0)** and the shares defined by **Q(x)** form a *2t*-sharing of the same value. Together, these give a valid *(t, 2t)*-sharing.

However, the expensive part of this approach is generating the initial **3t + 1** random values in *t*-shared form. Each party must act as a dealer and invoke AVSS multiple times, followed by a randomness-extraction step to ensure uniformity. Using the AVSS scheme, this costs **O(n³ log |F|)** bits per party and **O(n⁴ log |F|)** bits in total, which is prohibitively large.

The new approach reduces this cost by changing how the *(t, 2t)*-sharing is constructed. Instead of starting from **3t + 1** *t*-shared random values, it assumes access to only two shared values: a random value **r** that is *t*-shared, denoted **[r]ₜ**, and another random value **s** that is *(2t−1)*-shared, denoted **[s]₂ₜ₋₁**. Let **f(x)** be the degree-at-most-**t** polynomial defining **[r]ₜ**, and let **g(x)** be the degree-at-most-**2t−1** polynomial defining **[s]₂ₜ₋₁**.

From these two polynomials, a new polynomial **h(x) = f(x) + x·g(x)** is defined. The degree of **h(x)** is at most **2t**, and importantly **h(0) = f(0) = r**. Each party **Pᵢ** can locally compute its share of a *2t*-sharing of **r** by evaluating **h(i) = f(i) + i·g(i)**, using its existing shares from **[r]ₜ** and **[s]₂ₜ₋₁**. This produces a valid *2t*-sharing of the same secret **r**, while the original **t-sharing** of **r** is already available, yielding a *(t, 2t)*-sharing.

To obtain the initial **[r]ₜ** and **[s]₂ₜ₋₁**, each party acts as a dealer once to *t*-share a random value and once to *(2t−1)*-share another random value using the new AVSS scheme, followed again by a randomness-extraction step. This requires only **O(n² log |F|)** bits per party and **O(n³ log |F|)** bits in total.

Overall, this reduces communication by a factor of **Θ(n)** compared to the previous one. This improvement directly carries over to the AMPC protocol, yielding an overall **Θ(n)** reduction in communication complexity. The authors emphasize that this gain is not merely due to a clever recombination trick, but fundamentally relies on the availability of an AVSS scheme capable of *(2t−1)*-sharing, which was not possible in prior work.

### Packed secret-sharing in the asynchronous setting:
---

The AVSS schemes allow a dealer to share values using a polynomial of degree **d**, where **d** can be as large as **2t**. When the dealer is honest, the adversary learns at most **t** points on this polynomial, since it controls at most **t** parties. From the adversary’s perspective, this leaves **d + 1 − t** degrees of freedom in the polynomial, meaning that many different polynomials are still consistent with what the adversary observes.

This observation implies that a single degree-**d** polynomial can be used by an honest dealer to share not just one secret, but **d + 1 − t** independent secrets simultaneously. Each secret is embedded into the polynomial using coefficients or constraints that remain hidden from the adversary. This idea is conceptually the same as **packed secret sharing**, originally introduced in the synchronous setting, where multiple secrets are encoded into a single polynomial.

The key contribution of this work is that it realizes packed secret sharing in the **asynchronous** setting for the first time. Using this construction, the amortized communication cost of sharing a single field element is only **O(n)** field elements, even in the presence of an active (Byzantine) adversary. This matches the cost of classic Shamir secret sharing, which achieves **O(n)** communication only against a passive adversary. In other words, the scheme achieves optimal amortized efficiency despite operating under significantly stronger adversarial assumptions.

These packed AVSS schemes are especially useful in applications where parties need to share many values simultaneously. A canonical example is the **common coin** primitive, which is a crucial component of unconditionally secure asynchronous Byzantine Agreement protocols. In standard common coin constructions, each party needs to share or commit to **n** random values, typically by invoking **n** independent instances of an AVSS scheme, which is very expensive.

With packed secret sharing, a party can instead share multiple values using a much smaller number of AVSS invocations. Since one degree-**d** polynomial can carry **d + 1 − t** secrets, sharing **n** values requires only **ℓ = n / (d + 1 − t)** polynomials. By choosing the maximum allowed degree **d = 2t** and using the fact that **t = Θ(n)**, we obtain **d + 1 − t = t + 1 = Θ(n)**, which implies **ℓ = O(1)**.

As a result, each party can share all **n** required values by invoking the AVSS scheme only a constant number of times. This leads to a significant reduction in the overall communication complexity of the ABA protocol, without weakening security or resilience.

### 

The AVSS schemes are independent technique wise some of the techniques may lead to an improved AVSS scheme, which may further lead to a more efficient AMPC and ABA
protocol. Once there is a statistical/perfect AVSS scheme that generates d-sharing for
any **t ≤ d ≤ 2t**, we can obtain a statistical/perfect AMPC protocol by using the mentioned approaches. Both AVSS are **n = 4t +1**, have a d-sharing where **t<=d<=2t** and have a communication complexity of **O(ln2 log |*F*|)**.

## 1.3. Overview of Our AVSS Schemes
---

Assuming the AVSS shares a single secret, **D** should select a random bivariate polynomial, **F(x,y)**, where **x** and **y** have degree **t**, and **F(0,0) = s.** With **n = 4t + 1**, **D** can use a bivariate polynomial of degree at most **d** in **x** and **t** in **y** for all **d** with **t ≤ d ≤ 2t.** This forces us to be able to hide the secret in a bivariate polynomial of **degree-(d, t)**, achieving a *d*-sharing of the secret. It is not required, to do so, for the degree to be **(d,d)**, in fact, its beneficial that the degree of one of the variables remains **t**.

It starts with  **D** selecting a random bivariate polynomial **F(x,y)** of **degree-(d,t)** with **F(0,0) = s** and giving the polynomials **fi(x) =F (x,i)** of degree at most **d** and **gi(y) = F(i,y)** of degree at most t to every **Pi** . If **D** is honest, s is shared almost by a matrix **M**, that consists of **n * n** values. Every **Pi** receives the **ith** row and **ith** column of **M**, via **fi(x)** and **gi(y)**. This allows the secret to be *d*-shared through **f0(x) = F(x,0)** of degree **d**, where every **Pi** that is honest has **Shi = f0(i) = F(i,0) = gi(0)** of the secret **s**. Moreover, **Shi** is *t*-shared among **n** parties, through **gi(y)** where every **Pj** has the share-share **Shij = gi(j)** of the share **Shi**. This makes it so the polynomial **F** is shared in two levels. The at the top level, **s** is *d*-shared through **f0(x)**, and at the second level, every **Shi** is *t*-shared through the polynomial **gi(y)**. Reconstruction of the secret **s** can be ensured by asking every party to reveal its share of **s** and then by applying the error-correction on the revealed shares. Since **d <= 2t**, and **n = 4t +1**, the error correction is robust ensuring the reconstruction of **f0(x)**, and later, **s**. The second level shares no purpose for an honest **D** but it deals with a corrupted one. first, it ensures the **D** *d*-shares the secret. Second, it is required to complete *d*-sharing of **s**, since a corrupted **D** may not give the share **Shi** of **s** to every honest **Pi**.  We use the second level *t*-sharing of **Shi** to reconstruct **gi(y)** for the party **Pi** and this enables **Pi** to compute **Shi = gi(0)**. Since the second level is a t-sharing, we can guarantee the robustness if we ensure it has been done among a subset of **3t+1** parties, which can be somewhat easily ensured with additional primitives. If **d>t** and the polynomial was of degree **(d,d)** then it would be impossible to reconstruct such sharings robustly with only **3t+1** parties.

The dealer distributes the polynomials, the parties try to identify and agree on a common subset of **3t + 1** parties, say **CORE**, such that the **fi(x)** polynomials of the honest parties in **CORE** lie on a unique bivariate polynomial **~F**, of degree **(d,t)**. If **D** is honest, the **CORE** always exists, as there are **3t+1** honest parties, and as such **~F = F**. The issue is when **D** is dishonest. However, if a **CORE** can be established even with a corrupt **D,** the **D** has distributed a consistent polynomial to at least **2t +1** honest parties, the ones in the **CORE**. The consistent polynomials will uniquely define a polynomial **~F** of degree **(d,t)** which is **D**'s committed bivariate polynomial, and where **~s = ~F(0,0)**. To ensure that **~s** is *d*-shared, every honest **Pi** must possess **~Shi =~fi(0)** where **~f0(x) = ~F(x,0)** and **~s = ~f0(0)**. We try to complete the top level *d*-sharing of **~s** with the help of the second level *t*-sharing of each of its shares **~Shi**. Each **~Shi** of **~s** will be shared among the parties in **CORE** through the polynomial **~gi(y)** where **~gi(y)** is **~F(i,y)**, and has degree **t**. Since **|CORE| >= 3t + 1**, the parties can send their share-share of **~Shi** to **Pi**, and enable **Pi** to reconstruct robustly **~gi(y)**.

Even though **D** distributes information on a bivariate polynomial of **degree-(d, t)** where **d** may be greater than **t**, the parties are required to reconstruct polynomials of degree
at most t in order to obtain their shares of the secret. The main crux becomes agreeing on **CORE**. Once **CORE** is agreed upon *d*-sharing can be completed by reconstructing the second level *t*-sharing of the shares, which is committed to the parties in **CORE**. there are two methods of identifying the **CORE**, random checks on the polynomial given by **D**, having a negligible chance of incorrectly establishing the **CORE**, for the statistical AVSS, and the ACS, without any error, which results in the perfect AVSS.

# 2. Definitions and Preliminaries
---
## 2.1 Model
---

A completely asynchronous network is considered, where there are n = 4t +1 parties P = {P1, . . . , Pn}, connected by pairwise secure and authenticated channels. Each P is a probabilistic polynomial time Turing machine. There is a a computationally unbounded adversary A who can actively corrupt at most t out of the n parties and make them behave in any arbitrary manner during the execution of a protocol. Messages sent on the network have an arbitrary but finite delay and their order of arrival isn't guaranteed. in worst case, A can schedule the delivery of every message, without ever having access to its contents.

A computation is a series of atomic steps, where a party is active in each step. A party activates when receiving a message, performs the computations and possibly outputs a message. The order of steps is decided by a scheduler which A may also control. A party ends computation when it reaches an hat state. A protocol execution is complete if all honest parties terminate the protocol, after a finite number of steps. The adversary, usually considered static, can also be bested even if acting ad adaptive, where it can decide which parties to corrupt after analyzing previous executions.

## 2.2 Definitions
---

The computations are done over a finite field *F*. For the perfect protocols we require |*F*| > n, and for the statistical ones, F = GF(q), where q > max (n, 2<sup>k</sup>), such that k = log 1/e , for a given error parameter e. Also its assumed n = poly(k) so every field element can be represented by log |*F*| bits.

Let (Sh, Rec) be a pair of protocols for the n parties, where a dealer D ∈ P has a private input s ∈ F for Sh. We say that (Sh, Rec) is an AVSS scheme if the following requirements hold for every possible A : termination, correctness, secrecy. Which can easily be extended for packed secrets, a secret with l elements from *F*.

If an AVSS scheme satisfies the termination and the correctness condition with a probability at least (1 − e), for a given error parameter e , then such a scheme is called a statistical AVSS scheme. On the other hand,if the termination as well as the correctness condition is satisfied with probability 1 then such a scheme is called a perfect AVSS scheme. There is no compromise on the secrecy property.

A value s ∈ F is said to be d-shared among P if there exists a polynomial over F, say f (x), of degree at most d, such that f (0) = s and every (honest) party Pi holds a share Shi of s, where Shi = f (i). We denote by [s]d the vector (Sh1, . . . , Shn ) of shares of s. A value s ∈ F is said to be (t, 2t)-shared among the n parties, denoted as [s]t,2t , if s is both t-shared and 2t-shared, among the n parties. Also, d-sharing is linear in the sense that by applying any linear function to d-sharings, we obtain a d-sharing as the output. This allows the parties to locally compute any linear function of d-shared values. A bivariate polynomial F (x, y) over F has degree-(d, t) if the degree of x in F (x, y) is at most d and the degree of y in F (x, y) is at most t.

Let F : Fn → Fn be a publicly known function and let party Pi have a private input x i ∈ F.
Any asynchronous multiparty computation consists of three stages. In the first stage,
each party Pi commits its input. Even if Pi is faulty, if it completed this step, then it is
committed to some value (not necessarily xi). Let x′i be the value committed by Pi . If
Pi is honest then x′i = x i . Then the parties agree on a common subset C of at least n − t
committed inputs. In the last stage the parties compute F(y1, . . . , y n ), where y i = x′i if Pi ∈ C, otherwise y i = 0. An asynchronous protocol  among the n parties for computing the function F is called an AMPC protocol if it satisfies the following conditions for every possible A: termination, correctness, secrecy.

If an AMPC protocol satisfies the termination and the correctness condition with probability at least (1 − e), for a given error parameter e, then such a protocol is called a statistical AMPC protocol. On the other hand, if the termination as well as the correctness condition is satisfied with probability 1 then such a protocol is called a perfect AMPC protocol. There is no compromise in the secrecy property.

## 2.3 Primitives used
---

### [Asynchronous Reliable Broadcast](Asynchronous Reliable Broadcast.md)
---

### [Agreement on Core Subset](Agreement on Core Subset.md)
---

### [On-line Error Correction](On-line Error Correction.md)
---

### Randomness Extraction
---

### [(n,t)-Star]((n,t)-Star.md)
---

# 3. AVSS for Sharing a Single Secret
---

# 5. Protocols for Generating (t, 2t)-Sharing of *l* Values
---
Once AVSS handles *d*-sharing ***l*** values for any given **d**, where **d<= 2t**, it simply needs to follow that we must have a way to *(t,2t)*-share said values.  In order to so **D** must first perform the *t*-sharing of all values. After, **D** must *(2t -1)*-share ***l*** random values as well. Each 2 value pairs are shared by 2 polynomials, corresponding to *f(x)* and *g(x)*, with degree t and 2t -1, respectively, and every Pi holding shares  fl(i) and gl(i) of sl and rl respectively.  

The polynomial hl(x) 0 fl(x) + x gl(x) has degree at most 2t and its constant term is sl. Every party can locally compute hl(i) = fl(i) + i · gl(i). This results in sl being (t,2t)-shared through the polynomials fl(x) and hl(x).

To do so, the dealer invokes 2 instances of the sharing protocol, to deal ***l*** values.  All security properties come from the called protocol, either S-MS-Sh or P-MS-Sh, the only difference being if the result has a negligible error or not.
# 6. AMPC Protocols with n = 4t + 1
---
We can only design the AMPC protocol after being able to generated the *(t,2t)*-sharings used for the various needs. The protocol is to be divided into a sequence of 3 phases, where depending if those phases use protocols with negligible error or no error, the AMPC will be considered statistical or perfect, respectively.

**F** is a function over *F* represented by an arithmetic circuit over *F* that contains input, linear, multiplication, random and output gates of bounded fan-in. Multiplication gates have fan-in two and random gates have fan-in zero. Ci, Cl, Cm, Cr and Co represent the number of the respective gates: input, linear, multiplication, random, output. And IGate, LGate, MGate, RGate and OGate represent a gate of the respective kind. **F** is *F* <sup>n</sup> -> *F* <sup>n</sup>. Each **Pi** has its own input, and n parties receive the output. This implies that both Ci and Co are n.

The three phases are:
1. Preparation phase: preparing the raw material used for the evaluation of the circuit, that is, generating the *(t,2t)*-sharing of Cm + Cr uniformly random values from *F* that are information-theoretically secure.
2. Input phase: parties share their inputs for the **F**, *t*-sharing the inputs, and agreeing on a subset of at least **n-t** parties whose inputs will be utilized.
3. Computation phase: based on the inputs the circuit is evaluated gate by gate, with each output remaining *t*-shared among all parties.

## 6.1 Preparation Phase
---
Parties interact to generate the *(t,2t)*-sharing of the **Cm + Cr** values. These values must remain information-theoretically secure. Every Pi acts as a dealer and *(t,2t)*-shares **Cm + Cr / n - 2t** values. The parties then agree on a common subset C, of at least n-t parties, that correctly shared the values. The parties then apply the randomness extraction function on the values shared by the parties in C in order to output all the **Cm + Cr** *(t,2t)*-shared values. Since the values shared are random and secure, the output and end result will be also random and secure. The mentioned protocols for this phase are S-Preparation and  P-Preparation. One instance of [ACS](Agreement on Core Subset.md) must be run for the phase to properly work.

## 6.2 Input Phase
---
In this phase each Pi will *t*-share its input **xi** by running the correspondent AVSS protocol. If S-MS-Sh was invoked, we call on the S-Input, otherwise if P-MS-Sh is used, we call on P-input. Since we are on an asynchronous network, we must run the [ACS](Agreement on Core Subset.md) protocol again. The parties will from now on consider the inputs of only the Pi that is in C, and assume the others as a default t-sharing of 0.

## 6.3 Computation Phase
---
The protocol in this phase is the same for both types of AMPC protocols. The circuit is now evaluated gate by gate, with all intermediate values remaining *t*-shared. As soon as the parties get their shares of the input values for the gate they join the evaluation. Linear gates can be evaluated locally. With every random gate on random *(t,2t)*-sharing is associated, used directly as the output of the gate. With the multiplication, one *(2,2t)*-shared value is also used, in order to compute the *t*-sharing of the product. The protocol used by both parts is called [Computation](Computation.md)

## 6.4 Statistical AMPC Protocol with n = 4t + 1
---
The S-AMPC protocol follows three steps:
1. Invoke S-Preparation
2. Invoke S-Input
3. Invoke Computation
It requires a communication of **O(((Cl + Cm + Cr + Co )n² + n⁴)log |*F*|)** bits, a broadcast **O(n³log |*F*|)** bits and two invocations of ACS.

## 6.5 Perfect AMPC Protocol with n = 4t + 1
---
The perfect P-AMPC protocol follows three steps:
1. Invoke P-Preparation
2. Invoke P-Input
3. Invoke Computation
It requires a communication of **O((Cl + Cm + Cr + Co)n² log |*F*|)** bits, a broadcast of **O(n³log |*F*|)** bits and two invocations of ACS.
# 7. Packed Secret-Sharing: Another Perspective of Our AVSS Schemes
---
The share protocol can *d*-share a single secret. If **D** is honest then: there exists a polynomial *fx(0)* = F(x,0) of degree at most d, and every **Pi** holds Shi = f0(i); **A** knows at most t distinct points on f0(x) and lacks d+1-t additional points on f0(x) to interpolate the polynomial. This suggests that f0(x), has a certain "degree of freedom", referring to te d+1-t points that **A** does not know. **D** may be able to share d+1-t secrets simultaneously, in the information-theoretic sense.

 If **D** is corrupted, there exists d+1-t values to which it committed. Letting there b ***k*** values **D** wants to share among the parties, where k=d+1-t. D selects a polynomial *f(x)* over *F* with degree at most **d**. The polynomial simply follows the condition that f(0) = s1, f(-1) = s2, ... , f(-k+1)= sk. **D** then selects the bivariate polynomial, that ensures that  F(x,0) = f(x), meaning f0(x) = f(x). **D** would then invoke the share protocol, using **F(x,y)** as mentioned before. If **D** is honest, by the termination property every honest **Pi** will eventually terminate the protocol, with its share **Shi = f(i)**. This share is the share for all the ***k*** secrets mentioned above, which are information-theoretically secure, since f(x) has degree at most **d** and **A** gets at most **t** points. Even if **D** is corrupt, there are ***k*** values committed to at the end. 
 
To recover all secrets, the parties execute the [Rec](Rec.md) protocol, from which follows that every honest party will eventually reconstruct f(x)  correctly and recover the secrets. 

This idea also works when multisharing, where we use ***l*** independent bivariate polynomials, of degree (d,t) to share d+1-t values. This implies being able to share a total of ***l* * (d+1-t)** values all at once. 

Setting d = 2t, we can share L(t+1) = O(ln) values, which incurs in a communication of **O(ln²log|*F*|)** bits and a broadcast of **O(n²log|*F*|)**. We can ignore ***l*** in broadcast, since the protocol is independent from the value, and as such, the amortized cost of sharing a single secret is **O(n log |*F*|)**. Which means there is an improvement when interpreting this as a packet secret sharing scheme. It is important to note that the cost of sharing a single secret tolerating an active adversary~



#incomplete #paper  