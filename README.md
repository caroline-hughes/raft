Raft: https://raft.github.io/raft.pdf.

Command line spec

```./3700kvstore <UDP port> <your ID> <ID of second replica> [<ID of third replica> ...]]```

Our high-level approach:
1. Read through the Raft paper and use additional learning materials about Raft to understand the nuances around elections and log replication.
2. Write bare bones version of the protocol and understand the 
3. Implemented timeouts which can trigger elections, and the logic for a ```RequestVote RPC```, and casting a vote
4. Implemented logic for a heartbeat ```AppendEntries```  RPC, and reception by followers
5. Added support for ```get()``` and ```put()``` client requests, implemented necessary corresponding follower redirection logic
6. Added additional Replica class fields along the way as necessary to keep track of metadata for Followers, Candidates, and the Leader

Challenges:
- Adding the next layer of logic and breaking previous tests. For example:
    - in adding the conditions for log replication and logic for retrying a failed ```AppendEntries``` during reconciliation, earlier passing tests would break
    - in removing our puts_queue (which let's us complete put()s one at a time monotonically) to attempt supporting >1 entry per ```AppendEntries``` RPC, earlier passing tests would break
- Debugging was more difficult in this project due to the nature of it being a distributed system
    - The parallel nature of Raft made it harder to see the true order of events for replicas. We did use ```select()``` in our ```run```, which helped, but still found it hard to parse through long terminal output efficiently.
- Time has been the biggest problem for us in getting our Raft program to be a comprehensive version of the protocol. 
    - With other finals and final projects, we did not have enough time to finish. Ultimately, we feel we understand the Protocol as outlined by the paper quite well, but chose to prioritize getting points for tests that pass versus submitting our more comprehensive version of the code.
  

Testing:
- We tested primarily by running individual configs via the command line, and inspecting output to understand why bugs were occurring. 