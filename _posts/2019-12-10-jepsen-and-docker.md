---
layout: post
title: Jepsen, Docker, and Kubernetes
---

Aphyr's Jepsen project took the distributed systems world by storm in 2013. Jepsen showed us a way to measure the consistency guarantees of systems (mostly data stores) many of us use on a daily basis. The impact was revolutionary. Jepsen found the reality of consistency guarantees often fell short of the lofty marketing promises. In [many cases](https://jepsen.io/analyses), the discrepancies identified by Jepsen led to the vendors it "caught" fixing them or dialing down the strength of their claims. 


### Test Yo'self!
In Jepsen, the community wasn't just given a new, database-y version of [Fight Back!](https://en.wikipedia.org/wiki/Fight_Back!_with_David_Horowitz). It was also given a way to verify the correctness of it's own distributed systems. 

![Treat Yo'self!](/assets/treat-yo-self.gif)


I found this especially interesting since I'm experimenting with building distributed systems in Bloom, a proof-of-concept language based on the [CALM Theorem](https://rise.cs.berkeley.edu/blog/an-overview-of-the-calm-theorem/). 

### Stay Calm
To prove that Bloom programs can maintain correctness in the face of unpredictable networks, an [early version](http://db.cs.berkeley.edu/jmh/calm-cidr-short.pdf) of the 2011 Bloom mentions a demo with a deliberately evil network: 

`
For demonstration purposes, we will interpose a demonic message delivery module, which operates at each server replica and delivers messages in a random “bad” order (e.g., deletions before insertions,checkouts before all insertions and deletions).
`

Jepsen's _nemesis_ takes a different approach to verifying correctness by injecting faults between database nodes by temporarily blocking connectivity between some (partitions), adding/removing/restarting nodes (flapping nodes), introducing clock skew (a different kind of reordering), and otherwise making life difficult for a poor distributed system. All while sending concurrent reads/writes or lock acquire/releases, whatever the system under test was designed to do and logging the results to a history log. Finally the history is analyzed to determine whether the a set of properties held true amidst the chaos.

### Industrial Strength
Jepsen could serve as a kind of [property-based testing framework](https://medium.com/criteo-labs/introduction-to-property-based-testing-f5236229d237) for distributed systems to help check behavior during development. In fact, at one of my previous jobs, a major distributed database being built from the ground up had an automated Jepsen test suite checked into the code to verify it's own consistency guarantees. 