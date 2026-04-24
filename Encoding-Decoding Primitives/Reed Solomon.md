A class of **error-correcting codes** that use polynomial interpolation to **reconstruct data** from incomplete or corrupted pieces. They enable systems to tolerate **erasures** (missing data) and **errors** (corrupted data) beyond simple polynomial interpolation.

RS ensures:
- **Erasure Correction**: Can recover original data if up to **n-k** pieces are missing
- **Error Correction**: Can detect and correct up to **(n-k)/2** errors

# How it works
---
1. **Prime Definition**:  For the proper functioning of the arithmetic, due to the exponential grow, a prime **p** bigger than the values of the messages must be picked. This value is used to define a <!--[finite field](Galois%20Fields.md)-->finite field used in the operations.
2. **Label the packets**: identify the packets to be sent in order to ease the operations
3. **Tolerance and interpolation**: Interpolate the polynomial using the labeled messages to be sent and define the tolerance, that is, the number of redundant packets to be sent. Using the interpolated polynomial, give the values for the redundant packets
4. **Send and receive**
5. **Recover**: As long as **t+1** packets arrive the polynomial can be reconstructed easily, and the missing values recovered by inputing the label values. If there is data corruption more steps need to be applied.
6. **Error correction**: First step is to identify the error location, then find it, then find the error values, correct them and finally reconstruct the original data.
# Thresholds
---
For us to withstand losing **k** packets, we must sent at least **t+k** packets. If we also want to correct **e** errors, we must send **2e + t** packets instead.

# Guarantee
---
This code ensures that the message and the secret can be correctly constructed later on even if parts of the message, certain packets, are lost or corrupted.
