Cryptographic protocol letting one party lock a secret value into a hidden, unchangeable commitment, then reveal it later to prove they committed to that specific value, preventing cheating by changing it.

CS ensures:
- **Binding**: Once you commit to a value, you cannot change it later. Can't produce a valid opening to a different value for the same commitment
- **Hiding**: The commitment reveals nothing about the committed value

# How it works
---
1. Commit Phase - The committer selects a message _m_ from the message space and generates a commitment _c_ using a commitment function and auxiliary randomness _r_. The commitment _c_ is transmitted to the verifier while keeping _m_ and _r_ private. This establishes a cryptographic binding to the value _m_ at time _t_.
2. Reveal Phase - At a later time _t' > t_, the committer discloses both the original message _m_ and the randomness _r_ (collectively called the "opening" or "decommitment"). The verifier executes a verification algorithm to check that the revealed values correctly correspond to the previously received commitment _c_.

The exact details depends on the [implementation](Commitment scheme variations.md)
# Guarantee
---
With a commitment scheme, we can verify that the information revealed by the committer is exactly what they originally locked in—they cannot fake it or change it after committing. The commitment acts as cryptographic proof that binds them to their original choice.