The (n,t)**-star** is a graph-theoretic structure used as a verification primitive to ensure that a dealer's shared secret is unique and reconstructible, in other words, correct.

(n,t)Star ensures:
- **Validity**:  If the dealer is honest, the reconstructed secret is guaranteed to be the original secret shared. An honest dealer's protocol always results in a valid star that all good parties can eventually find.
	- **Termination**: If the dealer is honest, every honest party will eventually finish the sharing phase. If any honest party completes the sharing phase, all honest parties will eventually complete both sharing and reconstruction.
- **Agreement**:If one honest party finds the star, then all the parties will find the star.
- **Uniqueness:** Finding a (n,t)-star ensures there's only one possible polynomial to be reconstructed.
- **Secrecy**: As long as the dealer is honest, malicious parties gain no information about the secret until the reconstruction.
- **Error Correction**:T he protocol can reconstruct the correct secret even if up to t parties send wrong or missing values. By using [On-line Error Correction](On-line Error Correction.md).

# How it works
---
1. **Distribution**: Dealer distributes a bivariate polynomial **h(x,y)** and shares to the party, **Pi**, **fi(x)=(i,x)** and **gi(x)=h(x,i)**.
2. **Cross-Check**: Parties exchange values with each other, fi(j) checks gj(i) and fj(i) checks gi(j). If both values match they broadcast message (OK,i,j).
3. **Trust Graph**: Each party maintains their local OK graph where edge i, j, exists if both ok'ed each other.
4. **Trust Core**: A party accepts the secret only when it identifies a pair of sets (C,D) where ∣C∣≥n−2t and ∣D∣≥n−t, such that every node in C is connected to every node in D, that is, when the party find the star in its graph
5. **Agreement on star**: When a star is found, the party sends it to other parties. Those parties will verify it against their own graph and check. Once that star is validated, the secret is accepeted as existing(correctly shared initially) but it might've been incorrectly shared between parties, or have any mistakes, in this case [OEC](On-line Error Correction.md) is used to reconstruct the secret correctly.
# Thresholds
---
Total parties must be **n > 4t**, C must have **n - 2t** nodes, and D must have **n-t** nodes.
# Guarantee
---
This structure guarantees that even if t parties are malicious, the set **D** will contain at least **2t+1** **honest** parties. Since only **t+1** correct points are needed to interpolate a polynomial of degree t, the **2t+1** correct points provided by the honest parties are more than sufficient to uniquely reconstruct the polynomial, even if the remaining t parties in **D** provide false values. This ensures that all honest parties will eventually reconstruct the identical, unique secret.

