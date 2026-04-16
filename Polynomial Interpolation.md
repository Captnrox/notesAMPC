A fundamental mathematical technique in [secret sharing](Secret%20Sharing.md) and **distributed computing** that allows reconstructing a polynomial, and, therefore, a secret from a sufficient number of points. It's the core mechanism behind  [Shamir's Secret Sharing](Shamir%27s%20Secret%20Sharing.md) and **threshold cryptography**.

PI ensures:
- **Uniqueness**: A single polynomial of degree t can be reconstructed from any **t+1** **distinct** points. A set of points can't generate 2 different polynomials of degree **t**.
- **Reconstruction**: Having **n>t** points allows for the reconstruction of the polynomial of degree **t**
- **Lagrange Basis**:  Any point can be expressed as a **linear combination** of the known points.

# How it works
---
### Lagrange Interpolation
---
1.  **Lagrange Polynomials**: Define the **n** required polynomials for the **n** points. Each polynomial must have the value 1 at a different point's x coordinate and 0 at the remaining point's x coordinates.
2. **Final Polynomial**:  For each previously defined polynomial multiply the corresponding *y* value and sum all the multiplied polynomials in order to obtain the interpolated one

# Thresholds
---
For the interpolation to work there must be available at least **t+1** points, where **t** is the degree of the polynomial

# Guarantee
---
After doing interpolation its guaranteed that a unique polynomial, and therefore an unique secret is recovered, corresponding to the points that were obtained and later shared. As well as secrecy for the value as long as one party does not have **t+1** shares of the polynomial to interpolated beforehand.