**Question 1:**

What are the two fundamental operations that a database needs to perform on the most basic level, and why do application developers need to understand storage engine internals even if they won't implement one from scratch?

```
The two most basic operations a database needs to perform are read and write. Application developers need to understand storage engine internals in order to make appropriate decisions on which engine matches their application demands. For example, if you are querying data for analysis, it is better to use an OLAP engine instead of an OLTP engine.
```

Great start! You've captured the core idea. Let me provide some feedback:

**Correct!** On the most fundamental level, a database needs to do two things: when you give it some data, it should store the data, and when you ask it again later, it should give the data back to you.[^1]

Your reasoning about why developers need to understand storage engines is also on the right track. You're probably not going to implement your own storage engine from scratch, but you do need to select a storage engine that is appropriate for your application, from the many that are available. In order to configure a storage engine to perform well on your kind of workload, you need to have a rough idea of what the storage engine is doing under the hood.[^2]

Your example about OLAP vs OLTP is good! The book emphasizes this further: In particular, there is a big difference between storage engines that are optimized for transactional workloads (OLTP) and those that are optimized for analytics.[^3]

**Nice work on question 1!** 

---

**Question 2:**

Explain the key difference between log-structured storage engines and B-trees in terms of how they handle data modification. What are the typical performance trade-offs between these two approaches?

```
Log-structured storage engines are append only immutable logs very optimized for write operations. On the other hand B-Trees are fixed page sized structure with references to other pages. It is optimized for read operations.
```

**Excellent answer!** You've identified the key distinctions. Let me provide some additional detail:

**Correct on the fundamental difference!** The log-structured indexes break the database into variable-size segments, typically several megabytes or more in size, that are written once and are then immutable. By contrast, B-trees break the database into fixed-size blocks or pages and may overwrite a page in place.[^1]

You're also right about the performance trade-offs. As a rule of thumb, LSM-trees are better suited for write-heavy applications, whereas B-trees are faster for reads.[^2] 

The book adds an important nuance: In a B-tree, looking up a key involves reading one page at each level. Since the number of levels is usually quite small, reads from a B-tree are generally fast and have predictable performance.[^3]

For writes, the log-structured approach benefits from **sequential writes**: The pattern of many small, scattered writes (as found in B-trees) is called random writes, while the pattern of fewer large writes (as found in LSM-trees) is called sequential writes. Disks generally have higher sequential write throughput than random write throughput, which means that a log-structured storage engine can generally handle higher write throughput on the same hardware than a B-tree.[^4]

**Great job on question 2!**

---

**Question 3:**

What is an SSTable (Sorted Strings Table), and what advantage does sorting by key provide over a simple append-only log with hash index?

```
An SStable is a sorted by key key/value pair that each key points to data blocks which is of few kilobytes a technique called spars index. It is better for reading than an append only log.
```

**Good effort!** You've got some of the core concepts, but let me clarify a few points:

**What is an SSTable?** One example of such a structure is a Sorted Strings Table, or SSTable for short. This file format also stores key-value pairs, but it ensures that they are sorted by key, and each key appears only once in the file.[^1]

**You're right about the sparse index!** You do not need to keep all the keys in memory. You can group the key-value pairs within an SSTable into blocks of a few kilobytes and then store the first key of each block in the index. This kind of index, which stores only some of the keys, is called sparse.[^2]

**Advantages over append-only log with hash index:**

1. **Better range queries:** Range queries are not efficient with hash maps. For example, you can't easily scan over all keys from 10000 to 19999—you have to look up each key individually in the hash map.[^3] But with sorted SSTables, range queries become efficient.

2. **Memory efficiency:** The hash table must fit in memory.[^4] With SSTables and sparse indexes, you only need to keep some keys in memory, not all of them.

3. **Compression:** Each block of records can also be compressed. Besides saving disk space, compression reduces the I/O bandwidth use, at the cost of using a bit more CPU time.[^5]

**Good progress on question 3!**

---

**Question 4:**

Describe the purpose and function of a Bloom filter in LSM storage engines. What is a 'false positive' in this context, and why is it acceptable?

```
LSM storage can be slow when reading long last updated key or a non existing keys because storage engine checks all segments. Bloom filter is an additional data structure added to each segment that calculate the hash of each key. When comparing a key against bloom filter if at least one of the hashes bit is set to 0 then the key does not exist. On the contrary false positive happens when all bits hashes are set to 1 but the key still not exist, there is a probability of 1% for 10 bits space.
```

**Excellent answer!** You've demonstrated a strong understanding of Bloom filters. Let me affirm what you got right and add a bit more detail:

**Purpose - Absolutely correct!** With LSM storage, it can be slow to read a key that was last updated a long time ago, or to attempt to read a key that does not exist, since the storage engine will need to check several segment files. To speed up such reads, LSM storage engines often include a Bloom filter in each segment, which provides a fast but approximate way of checking whether a particular key appears in a particular SSTable.[^1]

**How it works - Spot on!** For every key in the SSTable, we compute a hash function, producing a set of numbers that are then interpreted as indexes into the array of bits. We set the bits corresponding to those indexes to 1 and leave the rest as 0. When we want to know whether a key appears in the SSTable, we compute the same hash of that key as before and check the bits at those indexes.[^2]

**False positives - Correct!** If at least one of the bits is 0, we know that the key definitely does not appear in the SSTable. If the bits in the query are all 1s, the key is likely in the SSTable, but it's also possible that by coincidence all those bits were set to 1 by other keys. This case, where it looks as if a key is present even though it isn't, is called a false positive.[^3]

You mentioned the 1% probability for 10 bits - good recall! As a rule of thumb, you need to allocate 10 bits of Bloom filter space for every key in the SSTable to get a false-positive probability of 1%.[^4]

**Why false positives are acceptable:** If the Bloom filter says the key is present, we have to consult the sparse index and decode the block of key-value pairs to check whether the key really is there. If it was a false positive, we have done a bit of unnecessary work, but otherwise no harm is done—we just continue the search with the next-oldest segment.[^5]

**Outstanding work on question 4!**

---

**Question 5:**

What is write amplification, and why is it an important consideration for both LSM-trees and B-trees, especially in write-heavy applications?

```
I think write amplification is a way to write data into memory before persisting it a disk structure. For example postgresql implements Write Ahead Log (WAL) this allows database to reconstruct state in case of crash happens.
```

**Not quite!** You're thinking of the **write-ahead log (WAL)**, which is indeed important for crash recovery, but that's different from **write amplification**. Let me clarify:

**What is write amplification?** With any type of storage engine, one write request from the application turns into multiple I/O operations on the underlying disk. If you take the total number of bytes written to disk in a workload and divide that by the number of bytes you would have to write if you simply wrote an append-only log with no index, you get the write amplification.[^1]

**Examples of write amplification:**

- **In LSM-trees:** With LSM-trees, a value is first written to the log for durability, then again when the memtable is written to disk, and again every time the key-value pair is part of a compaction.[^2]

- **In B-trees:** A B-tree index must write every piece of data at least twice: once to the write-ahead log, and once to the tree page itself. In addition, writing out an entire page is sometimes necessary, even if only a few bytes in that page changed.[^3]

**Why is it important?** In write-heavy applications, the bottleneck might be the rate at which the database can write to disk. In this case, the higher the write amplification, the fewer writes per second it can handle within the available disk bandwidth.[^4]

Also: Besides affecting throughput, write amplification is also relevant for the wear on SSDs. A storage engine with lower write amplification will wear out the SSD less quickly.[^5]

**You were on the right track mentioning WAL** - it does contribute to write amplification, but write amplification is the **overall effect** of writing the same data multiple times, not the mechanism itself.

**Good effort on question 5!**

---

**Question 6:**

Explain how column-oriented storage works and why it is particularly well-suited for analytical queries in data warehouses compared to row-oriented storage.

```
Often analytical queries needs set of columns and an aggregate on them. The entire row is not needed. This save memory. Also the storage on disk is different than the row oriented storage. This allows compression to work best per column.
```

**Excellent answer!** You've captured the key concepts perfectly. Let me reinforce what you said with some supporting details:

**How column-oriented storage works:** The idea behind column-oriented (or columnar) storage is simple: instead of storing all the values from one row together, store all the values from each column together instead. If each column is stored separately, a query needs to read and parse only those columns that are used in that query, which can save a lot of work.[^1]

**Why it's well-suited for analytical queries - You're absolutely right!** A typical data warehouse query accesses only four or five of them at one time. A query accesses a large number of rows (every occurrence of someone buying fruit or candy during the 2024 calendar year), but it needs to access only three columns of the fact_sales table: date_key, product_sk, and quantity.[^2]

By contrast: In most OLTP databases, storage is laid out in a row-oriented fashion: all the values from one row of a table are stored next to one another. To process a query, a row-oriented storage engine still needs to load all those rows (each consisting of over 100 attributes) from disk into memory, parse them, and filter out those that don't meet the required conditions. That can take a long time.[^3]

**Compression - Spot on!** Besides loading from disk only those columns that are required for a query, we can further reduce the demands on disk throughput and network bandwidth by compressing data. Fortunately, column-oriented storage often lends itself very well to compression.[^4]

Take a look at the sequences of values for each column. There's a fair amount of repetition, which is a good sign for compression.[^5]

**Perfect answer on question 6!**

---

**Great job!** You've worked through all six questions on Storage and Retrieval. You demonstrated solid understanding of the core concepts, especially around LSM-trees, SSTables, Bloom filters, and column-oriented storage. 

Would you like to review any specific topics in more detail, or are you ready to move on to the next chapter?


