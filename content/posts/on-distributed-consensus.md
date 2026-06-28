---
title: "On Distributed Consensus"
date: 2025-11-12
tags: ["distributed systems", "raft", "consensus"]
---

Getting a cluster of machines to agree on anything is surprisingly hard. Not hard in the way that writing a compiler is hard — hard in the way that the problem keeps shifting under your feet the moment you think you understand it.

## The Core Problem

Imagine three servers that need to agree on the value of a single integer. Each can send messages to the others, but messages can be delayed, duplicated, or dropped. Any server can crash at any time.

How do you get them to agree?

The naive approach — "just pick the one with the highest value" — breaks immediately. What if two servers never receive each other's proposals? What if a server crashes mid-write and restarts with stale state?

## Raft's Answer

Raft sidesteps much of this by introducing a strong leader. At any given time, one node is elected leader and all writes go through it. The leader appends the write to its log, replicates it to a majority of followers, then commits.

```
Client → Leader → [Follower A, Follower B]
                  (waits for majority ack)
         Leader → commits → responds to client
```

The crucial insight: as long as a majority of nodes are alive and can communicate, the system makes progress. A majority quorum guarantees that any two quorums overlap — so a newly elected leader can always reconstruct the committed log.

## What Can Go Wrong

Raft is simpler than Paxos but still has sharp edges.

**Network partitions.** If the cluster splits into two groups, neither a minority nor a leaderless majority can make progress. This is by design — availability is sacrificed for consistency (the CP in CAP).

**Log divergence.** A leader can crash after committing locally but before replicating. The next leader must handle entries that some followers have and others don't, resolving conflicts by term number and log index.

**Spurious elections.** Followers that can't hear the leader (due to a slow network, not a crash) will start an election. This doesn't violate correctness, but it does interrupt availability. Tuning heartbeat intervals matters more than people expect.

## The Part Nobody Talks About

Most discussions of consensus stop at "logs get replicated." The harder operational question is what happens to your state machine during a leader transition.

If your leader crashes between committing entry 1000 and responding to the client, the client doesn't know whether its write succeeded. It will retry. Your state machine needs to be idempotent, or you need to track client request IDs and deduplicate — which is another distributed systems problem nested inside the first one.

Consensus is a foundation, not a solution. The interesting work starts once you have it.
