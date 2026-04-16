## Core Block
---
Inputs: [x₁], [x₂]
Control: [b] (secret random bit 0 or 1)

Outputs:
[y₁] = [b] × [x₂] + (1 - [b]) × [x₁]
[y₂] = [b] × [x₁] + (1 - [b]) × [x₂]

## Offline prerequisites
---
For the protocol to work, on one single iteration, it requires O(k/2 log k) secretly shared random bits as and O(k/2 log k) [Beaver triples](Beaver%27s%20trick.md) to be pre-computed. These will be used during the switch decision and the multiplications for those switches, respectively.

## Stages
---
### Stage 1:
Group messages into pairs, and apply the core block, a 2x2 switch to each pair of messages.

### Stage 2:
Regroup the messages again. To do so, usually the rule is the XOR with increasing powers of 2. The first group of pairs, on the first cycle, is decided by grouping those messages indexes that only differ on a certain bit, p.e.; on stage 1, pair messages whose index only varies on bit 1, the least significant; on stage 2, pair those that vary on the second bit, etc. 

The number of stages is decided by log<sub>2</sub>(k), being k the number of messages. If k isn't a power of two, some adjustments might have to be made, either by rounding up and using dummy messages, using a generalized variant or using smaller batches.

The servers don't decide on this dynamically either, the network topology is fixed and pre-programmed into the [MPC](MPC.md) circuit, making it so its all predetermined, hardcoded.

### Stage 3:
Use new random bits to apply the switch, permuting or not the paired messages. As mentioned above, go back to stage 2 and repeat log<sub>2</sub>(k) times in order to do all permutations.

## Execution
---
Each stage has k/2 switches, all the switches execute in parallel, and which one consumes one [Beaver triple](Beaver%27s%20trick.md) for its multiplication, as well as needs one communication round per layer for [batch reconstruction](.md).

## Properties
---
This network can implement any permutation with a single pass, which is highly advantageous, however, it is highly biased, making some iterations more likely than others, which sometimes requires iterating many times over the same set of messages, as its done with [Iterated-butterfly](Iterated-butterfly.md).