Protocol that allows a party in an **asynchronous** scenario to re-compute the polynomial, namely by recognizing when the received points reconstruct and define a unique interpolated polynomial of degree **t** and compute it, even if some malicious parties send invalid points.

OEC ensures:
- **Validity**: If the sender is honest then the recovered polynomial, and secret, is eventually recovered.
	- **Termination**: As long as the adversary bound isn't surpassed, eventually all honest parties terminate the protocol
- **Agreement**: If agreement succeeds, all honest parties recover the same polynomial.
- **Self-healing**: Errors in one round do not propagate irrecoverably.
- **Local correction**: Each party can correct errors independently using redundancy (erasure coding, voting, threshold signatures), no need for other parties participation.

# How it works
---
1. **Initialize error tolerance**: Start with **r = 0**, assuming no errors.
2. **Wait for enough data**: Wait until the accumulative set **I** contains at least **d + t + r + 1** points, where **d** = polynomial degree, **t** = max errors.
3. **Run error correction**: Take the current set **I** and run the Reed-Solomon error correction (EC) procedure with parameters **(d, r)**.
4. **Verify the result**: Check if the polynomial returned by EC actually fits at least **d + t + 1** points in **I**.            
5. **Increase tolerance and repeat**: If verification failed, increment **r** by 1, until it reaches **t**, eventually, allowing more errors, and go back to step 2. 
6. **Terminate**: If verification passes, terminate and output result.

# Thresholds
---
Protection against byzantine errors as long as **n = 4t + 1** and against lost messages or fail-stop errors as long as **n = 3t + 1**, as well as the [Reed Solomon](Reed%20Solomon.md)'s threshold **∣S∣≥d+2r+1**. The protocol will  in the worst case, need this error threshold to succeed.

# Guarantee
---
Using OEC ensures that all honest parties can independently recover the correct protocol state from incomplete or partially corrupted data, without needing to restart rounds or rely on retransmissions from failed send, as long as the threshold is respected.