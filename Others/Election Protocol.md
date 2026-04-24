# Failure Detector Ω
---
~~~
// Leader Election using Ω
Initial: leader = nil

Repeat forever:
   suspected = failure_detector.suspect_list()
   candidates = all_processes - suspected
   leader = min(candidates)  // smallest ID among non-suspected
   
   If Ω confirms leader L:
      Use L as commander for BA
~~~

# Randomized Leader Election
---
Each process generates a random number, they broadcast their number, after n-t numbers are received, the process with the lowest number is picked, if the leader may be faulty, the process repeats

# Token-Passing
---
Token circulates in a ring from P1-P2-...Pn-P1, the order can be picked by id, IP or be pre determined and fixed. Whichever process holds the token, its considered the leader. There are other mechanisms to deal with Byzantine attackers and other faults.