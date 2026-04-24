Multi Party Computation as a Service is a MPC paradigm where there is a separation between input and computing parties. These computing parties are usually a set of servers with different providers, making sure no provider surpasses the protocol thresholds in number of servers. These providers are abstracted or unknown from the client, which only provides inputs and awaits outputs,e.g..

Usually works in two phases:
- **Offline Phase** - Pre-processing: Continuously ran in the background by the servers in order to prepare enough randomness material to be used during computation, be it for random gates or Beaver's triples
- **Online Phase** : Determines which inputs to be inserted and starts the evaluation of the circuit where the pre-processed information will be used.

*Su, Y. et al. "Dumbo-MPC: Efficient Fully Asynchronous MPC with Optimal Resilience"*
*Lu, D. et al. "HoneyBadgerMPC and AsynchroMix: Practical Asynchronous MPC and its Application to Anonymous Communication"*