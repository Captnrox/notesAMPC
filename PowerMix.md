PowerMix is part of the HoneyBadgerMPC and it's one of the protocols used to permute the messages before broadcast.

## Offline prerequisites 
---
For the protocol to work, it requires to have the pre-computed shares of the random secret agreed between servers. For each input from a Client, *Ci*, all the servers agree on a *r*, a random value. For that value a secret share is prepared in order for each server to send their *Jr<sub>input</sub>K* to the client so it can proceed as usual in the masking of input. It is also requires another random value r<sub>pmix j</sub>, from which the servers compute the powers of the secret share using [RanDouSha](RanDouSha.md) and [Beaver triples](Beaver's trick.md) to calculate the powers, and obtain Jr<sub>pmix j</sub>K, Jr<sup>2</sup><sub>pmix j</sub>K, ...,  Jr<sup>k</sup><sub>pmix j</sub>K.

## Calculating the difference
---
After all servers receive the blinded message from the client, they calculate their share by doing 

 - **Jm<sub>j</sub>K = m' - Jr<sub>input</sub>K**

After, the server calculates a new secret shared value C, this value is obtained by

- **JCK = Jm<sub>j</sub>K - Jr<sub>pmix j</sub>K

This process is done without no need for communication since its simply just a linear operation.

## Public opening
---
After the servers finish the difference calculation they attempt to Open(JCK), which gives us C, this entails making the value known to all other servers. Awaiting the necessary number of shares, usually at least *2t+1*, C is reconstructed using [BatchRecPub](BatchRecPub.md), with which the value becomes public and common knowledge to all servers.

This, however, reveals no information to the attacker, since to know of m or even m' one would need to know the value of r. The main goal of this step is to retain anonymity, protecting the information from being known by a set of servers that may be dangerous. This is also a protection against [external eavesdroppers](external eavesdroppers.md).

Due to the properties of [MPC](MPC.md) there is no way for external attackers or a small coalition of servers to get any information about m or m'.

## Local computation of powers
---
The standard factoring rule says that :

- **Jm<sub>j</sub>K<sup>k</sup> - Jr<sub>pmix j</sub>K<sup>k</sup> = (Jm<sub>j</sub>K - Jr<sub>pmix j</sub>K) * (Σ<sup>k-1</sup><sub>l=0</sub> Jm<sub>j</sub>K<sup>i-1-l</sup> * Jr<sub>pmix j</sub>K<sup>l</sup>)**

From which we can derive the following formula:

- **{{Jm<sub>j</sub>K<sup>k</sup>}} = {{Jr<sub>pmix j</sub>K<sup>k</sup>}} + C * (Σ<sup>k-1</sup><sub>l=0</sub> Jm<sub>j</sub>K<sup>i-1-l</sup> * Jr<sub>pmix j</sub>K<sup>l</sup>)**
  
And from which, in turn, can be derived the following formula that can be used to turn [ComputePowers](ComputePowers.md) more efficient.

- **{{Jm<sub>j</sub>K<sup>k</sup> \* Jr<sub>pmix j</sub>K<sup>j</sup>}} = {{Jr<sub>pmix j</sub>K<sup>k+j</sup>}} + C * (Σ<sup>k-1</sup><sub>l=0</sub> Jm<sub>j</sub>K<sup>i-1-l</sup> * Jr<sub>pmix j</sub>K<sup>l+ j</sup>)
  
The powers are then calculated and put on a matrix from which are extracted the values Jm<sub>j</sub>K, Jm<sub>j</sub><sup>2</sup>K, ..., Jm<sub>j</sub><sup>k</sup>K. 

## Power sums
---
We now have to try and reconstruct the shuffled values. From the above, ti stems:

- **JS<sub>j</sub>K = Σ<sup>k</sup><sub>i=1</sub> Jm<sub>i</sub><sup>j</sup>K**

They do this sum locally for all their *k* messages from all the clients, which implies no communication is needed. The next step is, therefore, public reconstructing Sj, by using, once again [BatchRecPub](BatchRecPub.md) on the JS<sub>j</sub>K that each server holds. This is the second and final communication round, and after it, all servers now hold every the value for each Sj, its been made public.

## Reconstructing the messages
---
Having a polynomial f(x) = a<sub>k</sub>x<sup>k</sup> + a<sub>k-1</sub>x<sup>k-1</sup> + a<sub>1</sub>x + a<sub>0</sub> be a polynomial such that its roots, when f(x) = 0 are m<sub>1</sub>, m<sub>2</sub>, ..., m<sub>k</sub>. And we also have a<sub>k</sub> = 1, given that it is the coefficient of x<sup>k</sup> that comes from the expression (x - m<sub>1</sub>)(x - m<sub>2</sub>)(x - m<sub>k</sub>).

According to [Newtons Identity](Newtons Identity.md)'s we can get all the coefficients of the polynomial by solving a set of equations one by one. 

- **S<sub>1</sub> + a<sub>k - 1</sub> = 0**
- **S<sub>2</sub> + a<sub>k - 1</sub>S<sub>1</sub> + a<sub>k - 2</sub> = 0
- **S<sub>3</sub> + a<sub>k - 1</sub>S<sub>2</sub> + a<sub>k - 2</sub>S<sub>1</sub> + a<sub>k - 3</sub> = 0**
-  **...**

Once we know the coefficients we can then find the roots of f(x) = 0 with O(k²) complexity. This recovers the permutation/mixing set of the k input messages that are then broadcast. Since the return is done in [canonical ordering](Canonical ordering.md), the position of the messages may or may be not shuffled around, regardless of the order or who sent them, making it a good way to mask the order and break the link between the message and the sender.
