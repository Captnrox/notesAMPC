# Round Robin
---
~~~
For agreement instance i:
Commander = process (i mod n)
~~~

# Proof of Stake
---
User mainly in blockchain environments, where the commander picked is the validator with the highest stake or from a group with high stakes and picked at random.

# Multi Leader
---
All processes act as leaders, run multiple [BA](Byzantine%20Agreement.md) in parallel, combine the results ensuring more robust outcomes.

# Leader Election
---
Some [election protocol](Election%20Protocol.md) may be ran beforehand in order to decide the leader through more complex parameters.

# View Change Protocol
---
A leader is selected either randomly or with one of the above options. If the leader is suspected of being faulty a timeout occurs, nodes send "view-change" messages and once enough messages are sent the new commander in line, decided with the protocol, takes over and resumes the protocol (or restarts). (most common)