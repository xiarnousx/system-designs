Great! Let's start with some questions to test your understanding of Chapter 9: The Trouble with Distributed Systems:

**Question 1:**

What is a partial failure in a distributed system, and why does it make distributed systems fundamentally different from single-node systems?

```
Partial failure is when some parts of a distributed system fail unpredictably while others work; you may not know whether something succeeded or not. On a single computer, software usually deterministic either works or fails.
```

Excellent answer! You've captured the core concepts really well. Let me provide some feedback:

**Correct!** You've identified the key points:

1. **Partial failures are unpredictable**: In a distributed system, some parts may be broken in an unpredictable way while other parts work fine. This is known as a partial failure.[^1]

2. **Nondeterminism is the challenge**: The difficulty is that partial failures are nondeterministic—if you try to do anything involving multiple nodes and the network, it may sometimes work and sometimes unpredictably fail. As we shall see, you may not even know whether something succeeded![^2]

3. **Single computers are deterministic**: When the hardware is working correctly on a single computer, the same operation always produces the same result (it is deterministic). An individual computer with good software is usually either fully functional or entirely broken, but not something in between.[^3][^4]

Your understanding of this fundamental distinction is spot on! This nondeterminism and possibility of partial failures is what makes distributed systems hard to work with.[^5]

---

**Question 2:**

Explain the difference between time-of-day clocks and monotonic clocks. Which type should be used for measuring timeouts in distributed systems and why?

```
Time-of-day clocks are calendar clocks they drift from machine to another. Monotonic clocks are similar to stopwatches they measure elapsed time. In distributed systems, confidence intervals (earliest/latest) should be used to measure timeouts such as TrueTime/ClockBound.
```

Great answer with solid understanding! Let me provide some feedback and clarifications:

**Mostly correct!** You've got the key distinctions:

1. **Time-of-day clocks**: A time-of-day clock does what you intuitively expect of a clock: it returns the current date and time according to a calendar (also known as wall-clock time).[^1]

2. **Monotonic clocks for elapsed time**: A monotonic clock is suitable for measuring a duration (time interval), such as a timeout or a service's response time. The difference between two values tells you how much time elapsed between the two checks—more like a stopwatch than a wall clock.[^2][^3]

3. **Time-of-day clocks are problematic for timeouts**: If the local clock is too far ahead of the NTP server, it may be forcibly reset and appear to jump back to a previous point in time. These jumps, as well as similar jumps caused by leap seconds, make time-of-day clocks unsuitable for measuring elapsed time.[^4]

**One clarification needed:** **Monotonic clocks for timeouts**: In a distributed system, using a monotonic clock for measuring elapsed time (e.g., timeouts) is usually fine, because it doesn't assume any synchronization between different nodes' clocks and is not sensitive to slight inaccuracies of measurement.[^5]

Your mention of TrueTime/ClockBound is relevant but applies to a different use case—those are used for **ordering events across nodes** and **global snapshots**, not for measuring timeouts. Confidence intervals help with clock synchronization accuracy when you need to compare timestamps between machines.

Excellent work overall!

---

**Question 3:**

What is a fencing token and how does it prevent data corruption when a node incorrectly believes it still holds a lease?

```
Fencing token are a way to avoid data corruption when a node try to update data after it has been down/paused and another node take over and updated the data. To prevent this, the new node can use a fencing token to ensure that the old node's updates are ignored.
```

Good start! You've got the basic idea, but let me help clarify the mechanism. Here's the feedback:

**Partially correct!** You understand the problem fencing tokens solve, but the mechanism works a bit differently:

**How fencing tokens actually work:**

Every time the lock service grants a lock or lease, it also returns a fencing token, which is a number that increases every time a lock is granted (e.g., incremented by the lock service). We can then require that every time a client sends a write request to the storage service, it must include its current fencing token.[^1]

**The key distinction:** It's not that the "new node uses a token to block the old node." Instead, **the storage service enforces the rule**. For example, if client 1 acquires the lease with a token of 33, but then goes into a long pause and the lease expires, client 2 acquires the lease with a token of 34 and sends its write to the storage service. Later, client 1 comes back to life and sends its write including its token value, 33. However, the storage service remembers that it has already processed a write with a higher token number (34), so it rejects the request with token 33.[^2]

**In summary:** Every client includes its token with each write, and the storage service only accepts writes with monotonically increasing tokens. This way, zombie nodes (old leaseholders) can't corrupt data because their outdated tokens are rejected.

Great conceptual understanding—you just needed the implementation details!

---

**Question 4:**

Describe the three main system models used for timing assumptions (synchronous, partially synchronous, and asynchronous). Which model is most realistic for practical distributed systems?

```
Synchronous: bounded delays, bounded pauses, bounded clock error. Partially synchronous: usually synchronous but can have occasional unbounded delays. Asynchronous: no timing guarantees; extremely restrictive for some problems. Partially synchronous with crash-recovery is often the most useful real-world model.
```

**Excellent answer!** You've nailed all three system models and identified the most realistic one. Here's the detailed feedback:

**Completely correct!** Let me highlight what you got right:

1. **Synchronous model**: The synchronous model assumes bounded network delay, bounded process pauses, and bounded clock error. This does not imply exactly synchronized clocks or zero network delay; it just means you know that network delay, pauses, and clock drift will never exceed a fixed upper bound.[^1] However, the synchronous model is not a realistic model of most practical systems because unbounded delays and pauses do occur.[^2]

2. **Partially synchronous model**: Partial synchrony means that a system behaves like a synchronous system most of the time, but it sometimes exceeds the bounds for network delay, process pauses, and clock drift. This is a realistic model of many systems.[^3]

3. **Asynchronous model**: In this model, an algorithm is not allowed to make any timing assumptions—in fact, it does not even have a clock (so it cannot use timeouts). Some algorithms can be designed for the asynchronous model, but it is very restrictive.[^4]

4. **Most useful model**: For modeling real systems, the partially synchronous model with crash-recovery faults is generally the most useful. It allows for unbounded network delay, process pauses, and slow nodes.[^5]

Perfect understanding! You clearly grasp why the partially synchronous model strikes the right balance between realism and being able to design practical algorithms.

---

**Question 5:**

What is the difference between safety properties and liveness properties in distributed systems? Give an example of each.

```
Safety properties aim for correctness and security, while liveness is eventually something good happens. An example of safety is that a system should never return incorrect data. An example of liveness is that a system should eventually respond to a request.
```

**Excellent answer!** You've captured the essential distinction between safety and liveness. Let me provide detailed feedback:

**Correct!** You've understood both concepts well:

1. **Safety vs. Liveness (informal)**: Safety is often informally defined as "nothing bad happens," and liveness as "something good eventually happens."[^1]

2. **Liveness includes "eventually"**: A giveaway is that liveness properties often include the word "eventually" in their definition. (And yes, you guessed it—eventual consistency is a liveness property.)[^2]

**The more precise technical definitions:**

**Safety property**: If a safety property is violated, we can point to the particular point in time that it was broken (e.g., if the uniqueness property was violated, we can identify the particular operation in which a duplicate fencing token was returned). After a safety property has been violated, the violation cannot be undone—the damage is already done.[^3]

**Liveness property**: A liveness property works the other way around. It may not hold at a certain point in time (e.g., a node may have sent a request but not yet received a response), but there is always hope that it may be satisfied in the future (namely, by receiving a response).[^4]

Your examples are spot-on! "Never return incorrect data" is a safety property (uniqueness, monotonic sequence), and "eventually respond to a request" is a liveness property (availability).

Perfect understanding! 🎉

---

**Question 6:**

Explain how deterministic simulation testing (DST) differs from traditional fault injection testing, and describe one advantage it provides.

```
Deterministic simulation testing (DST) simulates testing for a distributed software on three levels 1) application level, 2) runtime level and 3) machine level. Fault injection verifies behavior under failures (chaos engineering) while DST verifies behavior under all possible interleavings of events. DST can find bugs that are hard to reproduce with traditional testing.
```

**Excellent answer!** You've demonstrated strong understanding of both techniques. Let me provide detailed feedback:

**Correct!** You've identified key distinctions:

1. **DST tests actual code, not a model**: Another formalization technique, which has become a popular complement to model checking and fault injection, is deterministic simulation testing (DST). It uses a similar state space exploration process to a model checker, but it tests your actual code, not a model.[^1]

2. **DST explores many scenarios**: In DST, a simulation automatically runs through a large number of randomized executions of the system. Network communication, I/O, and clock timing during the simulation are all replaced with mocks that allow the simulator to control the exact order in which things happen, including various timings and failure scenarios. This allows the simulator to explore many more situations than handwritten tests or fault injection could.[^2]

3. **Key advantage - replayability**: If a test fails, it can be rerun since the simulator knows the exact order of operations that triggered the failure—in contrast to fault injection, which does not have such fine-grained control over the system.[^3]

**Your three levels are spot on!** DST requires controlling nondeterminism via: **Application-level** (some systems are built from the ground up to make it easy to execute code deterministically), **Runtime-level** (languages with asynchronous runtimes provide an insertion point to introduce determinism), and **Machine-level** (rather than patching code at runtime, an entire machine can be made deterministic).[^4][^5][^6]

Outstanding work! You clearly understand how DST provides more systematic exploration and reproducibility compared to fault injection's chaos engineering approach. 🎉

---

**Great job!** You've worked through all six questions on this topic. You demonstrated solid understanding of distributed systems challenges including partial failures, clocks, fencing mechanisms, system models, safety/liveness properties, and testing techniques.

Would you like to move on to the next chapter, or would you like to discuss any of these topics in more depth?