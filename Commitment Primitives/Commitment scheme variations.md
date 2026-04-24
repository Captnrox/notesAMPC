# Feldman Commitments
---
Uses DLP's computational hardness to ensure the hiding property of the commitments.

1. Decide the polynomial
2. Pick 3 values, 2 primes p and q, where q is the greatest divisor of p-1 and g, the generator of the subgroup G
3. Commit by broadcasting all coefficients in the form: Ci = g<sup>(ai)</sup>
4. Sends share respectively
5. Each party verifies their share by doing ```g^(sᵢ) = ∏(j=0 to t) Cⱼ^(iʲ)```
6. If verification fails, party broadcasts a complaint, and if complaints surpass the threshold share and sharer are trashed. Otherwise the protocol resumes.
### Properties
---
- **Computational hiding**: Security relies on the hardness of a mathematical problem, an attacker with unlimited computing power could break it, but it's infeasible with realistic resources.
- **Perfectly binding**: The commitment cryptographically binds the committer to a single value, making it mathematically impossible to produce two different valid openings for the same commitment.
# Pedersen commitments
---
Uses DLP's computational hardness to ensure the hiding property of the commitments.

1. Decide on the polynomial and on the blinding polynomial, a(x) and b(x)
2. Chose a group G of prime order q, with generators g and h, with their log relationship **log_g(h)** unknown. The other parameters are public
3. Commit by broadcasting the coefficients in the form Ci = g<sup>(ai)</sup> * h<sup>(bi)</sup>
4. Sends respective shares
5. Each participant verifies their share by doing ```g^(sᵢ) · h^(s'ᵢ) = ∏(j=0 to t) Cⱼ^(iʲ)```
6. If verification fails, party broadcasts a complaint, and if complaints surpass the threshold share and sharer are trashed. Otherwise the protocol resumes.
### Properties
---
- **Perfect hiding**: The committed value is information-theoretically secure—even an adversary with unlimited computing power cannot determine what was committed to before it's revealed.
- **Computationally binding**: The commitment cryptographically binds the committer to a single value, making it computationally infeasible to produce two different valid openings for the same commitment.