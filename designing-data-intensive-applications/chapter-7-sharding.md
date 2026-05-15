**Question 1:**

What are the two main ways a distributed database typically distributes data across nodes, and what is the primary difference between them?

```
The two possible way of distributed database distributes data is by means of replications and sharding. Sharding is a way of having each node handle subset of dataset, on the other hand replication replicates the entire dataset per node. Sharding is more appropriate when deal with large volume of data.
```

**Excellent answer!** You've captured the key differences very well.

To summarize what you said with supporting details from the book: A distributed database typically distributes data in two ways: replication stores a copy of the same data on multiple nodes (discussed in Chapter 6), while sharding splits the data into smaller shards or partitions when there's so much data or such high write throughput that a single node cannot handle it, storing different shards on different nodes[^1][^2][^3].

You're absolutely right that sharding is a solution if the volume of data or the write throughput has become too great for a single node to handle, as it allows you to spread that data and those writes across multiple nodes[^4].

Great job identifying that fundamental distinction! Let's move on to the next question.

---

**Question 2:**

Explain the primary reason for sharding a database and describe at least two challenges or downsides that sharding introduces.

```
The primary reason for sharding is scalability.  One major drawback sharding introduce is searching by secondary index or join records that might be distributed across shards. Another challenge is consistency through transactions which requires distributed transaction which adds to the over all latency.
```

**Excellent work!** You've identified the main reason for sharding and captured two significant challenges very accurately.

You're spot on that the primary reason for sharding a database is scalability[^1]. More specifically, sharding is one of the main tools we have for achieving horizontal scaling (a scale-out architecture)—that is, allowing a system to grow its capacity not by moving to a bigger machine, but by adding more (smaller) machines[^2].

Your first challenge is correct: sharding often works well for key-value data, where you can easily shard by key, but it's harder with relational data, where you may want to search by a secondary index or join records that might be distributed across different shards[^3].

And your second challenge about distributed transactions is also accurate: A write may need to update related records in several shards. While transactions on a single node are quite common, ensuring consistency across multiple shards requires a distributed transaction. Distributed transactions are available in some databases, but they are usually much slower than single-node transactions and may become a bottleneck for the system as a whole[^4].

Great understanding of the trade-offs! Let's continue.

---

**Question 3:**

What is a 'hot spot' or 'hot shard' in the context of sharding, and what can cause it to occur?

```
Hot spot or Hot shard is when a single shard gets all or most of the write queries leaving rest of the shards idle. It cause data skew across shards. Hot spot often has direct coloration with key range sharding.
```

**Very good!** You've captured the essence of what a hot spot is and identified a key cause. Let me provide some feedback with supporting details from the book.

You're absolutely right about the definition: If the sharding is unfair, so that some shards have more data or queries than others, we call it skewed. In an extreme case, all the load could end up on one shard, so 9 out of 10 nodes are idle, and your bottleneck is the single busy node. A shard with disproportionately high load is called a hot shard or hot spot[^1].

Your point about key-range sharding is correct. A downside of key-range sharding is that you can easily get a hot shard if there are a lot of writes to nearby keys. For example, if the key is a timestamp, then the shards correspond to ranges of time—for example, one shard per month. If you write data from the sensors to the database as the measurements happen, all the writes will end up going to the same shard (the one for this month), so that shard will be overloaded with writes while others sit idle[^2].

One additional detail: hot spots can also occur due to workload characteristics, not just the sharding scheme. If one key has a particularly high load (e.g., a celebrity in a social network), we call it a hot key[^3]. This can happen regardless of the sharding approach used.

Excellent understanding! Let's move to the next question.

---

**Question 4:**

Compare key-range sharding and hash-based sharding. What are the main advantages and disadvantages of each approach?

```
Key-range sharding is a technique to split shards by minimum/maximum key ranges, example encyclopedia partitioning. The disadvantage is that it easily creates hot spot shards. A hash based sharding it applying a hash function say md5 to a key this has an advantage of uniformly distribute data across shards.
```

**Great answer!** You've correctly identified both sharding techniques and their key characteristics. Let me provide some additional details to round out your understanding.

You're right about key-range sharding: One way of sharding is to assign a contiguous range of partition keys (from a minimum to a maximum) to each shard, like the volumes of a paper encyclopedia[^1]. 

The **advantages** of key-range sharding include: Within each shard, keys are stored in sorted order. This has the advantage that range scans are easy, and you can treat the key as a concatenated index in order to fetch several related records in one query[^2]. This is a key benefit you didn't mention—it makes range queries very efficient!

You correctly identified the hot spot issue as a disadvantage: A downside of key-range sharding is that you can easily get a hot shard if there are a lot of writes to nearby keys. For example, if the key is a timestamp, all the writes will end up going to the same shard (the one for this month), so that shard will be overloaded with writes while others sit idle[^3].

For **hash-based sharding**, you're correct: A common approach is to first hash the partition key before mapping it to a shard. A good hash function takes skewed data and makes it uniformly distributed[^4].

However, there's an important **disadvantage** to hash sharding: The downside compared to key-range sharding is that range queries over the partition key are not efficient, as keys in the range are now scattered across all the shards[^5].

Excellent work! Moving on to the next question.

---

**Question 5:**

What are the two main approaches to handling secondary indexes in a sharded database, and what is the key trade-off between them?

```
There are two approaches for handling secondary indexes which are local secondary index and global secondary index. Local secondary index is per shard where as global secondary index is across shards per key column. In case you want to obtain the full record with secondary index a visit has to be made to the containing shard.
```

**Very good!** You've correctly identified both approaches and made an important observation. Let me provide some feedback with additional details about the key trade-offs.

You're absolutely right about the definitions: In the first indexing approach, each shard independently maintains its own secondary indexes, covering only the records in that shard. Whenever you write to the database—to add, remove, or update a record—you need to deal with only the shard containing the record that you are writing. For that reason, this type of secondary index is known as a local index[^1].

And for global indexes: Rather than each shard having its own local secondary index, we can construct a global index that covers data in all shards. A global index must also be sharded, but it can be sharded differently from the primary-key index[^2].

Here's the **key trade-off** between them:

**Local secondary indexes:** If you want all the results and don't know their partition key in advance, you will need to send the query to all shards and combine the results you get back, because the matching records might be scattered across all the shards[^3]. So writes are simple (only one shard), but reads can be expensive.

**Global secondary indexes:** Global indexes have the advantage that a query with a single condition (such as color = red) needs to read from only a single shard to fetch the postings list[^4]. However, writes are more complicated than with local indexes, because writing a single record might affect multiple shards of the index (every term in the document might be on a different shard)[^5].

So the trade-off is essentially: **local indexes optimize for writes (simple), global indexes optimize for reads (simple)**.

Excellent progress! One more question to go.

---

**Question 6:**

Describe three different approaches to routing requests to the correct shard in a distributed database system.

```
There are three types of request routing when dealing with distributed databases. A client connect to a random node and it either serves the request or redirect to the node that has the data. A routing tier between the client and nodes that directly maps request to the appropriate nodes containing the shards. Another approach is to directly connect the client to the node based on the key provided. The routing tier may use a service discovery approach to make it relevant.
```

**Excellent answer!** You've accurately described all three routing approaches. Let me provide some supporting details from the book.

You're spot on with all three approaches, which are outlined in Figure 7-7: (1) Allow clients to contact any node (e.g., via a round-robin load balancer). If that node coincidentally owns the shard to which the request applies, the node can handle the request directly; otherwise, it forwards the request to the appropriate node, receives the reply, and passes the reply along to the client. (2) Send all requests from clients to a routing tier first, which determines the node that should handle each request and forwards it accordingly. This routing tier does not itself handle any requests; it acts only as a shard-aware load balancer. (3) Require that clients be aware of the sharding and the assignment of shards to nodes. In this case, a client can connect directly to the appropriate node, without any intermediary[^1][^2][^3][^4].

You also made a great observation about service discovery! This problem is called request routing, and it's very similar to service discovery[^5]. The book notes that many distributed data systems rely on a separate coordination service such as ZooKeeper or etcd to keep track of shard assignments. Each node registers itself in ZooKeeper, and ZooKeeper maintains the authoritative mapping of shards to nodes. Other actors, such as the routing tier or the sharding-aware client, can subscribe to this information in ZooKeeper. Whenever a shard changes ownership, or a node is added or removed, ZooKeeper notifies the routing tier so that it can keep its routing information up-to-date[^6].

---

**Great job! You've worked through six questions on Chapter 7: Sharding** and demonstrated a solid understanding of the key concepts including sharding approaches, hot spots, secondary indexes, and request routing. 