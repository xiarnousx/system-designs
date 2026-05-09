**Question 1:**

What is the difference between backward compatibility and forward compatibility? Explain why each one is important in evolving systems.

```
Backward compatibility is when new application code able to perform and read old data, while forward compatibility is when application new code able to perform and read new data. Often when deploying an application using rolling upgrades it becomes relevant for both backward and forward compatibility be achieved.
```

Good start! You've got the right general idea, but let me help clarify a small but important detail in your answer.

You correctly identified **backward compatibility** – newer code can read data written by older code[^1]. That part is spot on!

However, **forward compatibility** is actually the reverse: older code can read data written by newer code[^2]. You mentioned "new code able to perform and read new data," but forward compatibility is specifically about *old* code being able to handle *new* data formats.

You're absolutely right about the importance during rolling upgrades! Old and new versions of the code, and old and new data formats, may potentially coexist in the system at the same time[^3], which is why you need to maintain compatibility in both directions[^4].

The book also notes an interesting point: Backward compatibility is normally not hard to achieve. As the author of the newer code, you know the format of data written by older code, so you can explicitly handle it[^5]. However, forward compatibility can be trickier, because it requires older code to ignore additions made by a newer version of the code[^6].

Great understanding of the concept overall!

---

**Question 2:**

Why do binary encoding formats like Protocol Buffers and Avro use field tags or names instead of including field names in every encoded record? What advantages does this provide?

```
Binary encoding formats like Protocol Buffers and Avro, use field tags or names instead of including field names in every encoded record is reduce the compress the document size. The advantage is faster transfer and lower storage space.
```

Excellent! You've captured the core advantage perfectly!

You're absolutely right that they can be much more compact than the various "binary JSON" variants, since they can omit field names from the encoded data[^1]. This does lead to faster transfer and lower storage space, just as you said.

Let me add some specific context from the chapter to illustrate just how significant this is:

Encoding Example 5-2 using a Protocol Buffers encoder requires 33 bytes[^2], while the binary encoding [using MessagePack, a binary JSON format] is 66 bytes long, which is only a little less than the 81 bytes taken by the textual JSON encoding[^3]. And if we encode our record (Example 5-2) using [Avro] schema, the Avro binary encoding is just 32 bytes long—the most compact of all the encodings we have seen[^4].

So Protocol Buffers achieves about 50% compression compared to MessagePack, and Avro is even more efficient!

The key mechanism: Unlike Figure 5-2, this example has no field names (userName, favoriteNumber, interests). Instead, the encoded data contains field tags, which are numbers (1, 2, and 3). Those are the numbers that appear in the schema definition. Field tags are like aliases for fields—they are a compact way of indicating the field we're talking about, without having to spell out the field name[^5].

Great answer!

---

**Question 3:**

In Protocol Buffers, what happens when old code reads data written by new code that contains a new field the old code doesn't know about?

```
In Protocol Buffer we can simply add new field with new unique tag to the document. Also we keep the old field in the document intact. This way new code can simply ignore the old field and the new code can still reference it.
```

You're on the right track, but I think there might be a bit of confusion about which code is doing what. Let me clarify:

The question asks: what happens when **old code** reads data written by **new code** that has a new field?

Your answer talked about new code ignoring old fields, but it's actually the opposite scenario! Here's what actually happens:

You can add new fields to the schema, provided that you give each field a new tag number. If old code (which doesn't know about the new tag numbers you added) tries to read data written by new code, including a new field with a tag number it doesn't recognize, it can simply ignore that field[^1].

The clever part is how it does this: The datatype annotation allows the parser to determine how many bytes it needs to skip while preserving the unknown fields[^2]. This way, old code can read records that were written by new code[^3], maintaining forward compatibility.

You were correct that you add new fields with new unique tags! And you're also right that we want to keep fields intact. The book mentions that the desirable behavior is usually for the old code to keep the new field intact, even though it wasn't able to interpret it[^4], which helps avoid data loss when the old code updates and writes the record back.

Does this clarify the scenario?

---

**Question 4:**

Explain the difference between how Protocol Buffers and Avro handle schema evolution. What are the key differences in their approaches?

```
Protocol Buffers specify new fields with new tags that needs to be unique within the document. On the other hand Avro creates to schema definition language one for read and one for write and in order to deduct changes Avro compares the read schema with the write schema given that schemas are versioned. The challenge that Avro solves is making it easier to generate schemas on the fly using static compiled languages.
```

Great answer! You've identified several key differences. Let me help refine and add some important details:

**Protocol Buffers:**
You're absolutely correct! Field tags are critical to the meaning of the encoded data. You can change the name of a field in the schema, since the encoded data never refers to field names, but you cannot change a field's tag, since that would make all existing encoded data invalid[^1].

**Avro's Two-Schema Approach:**
You nailed this concept! The writer's schema is what's used for encoding, and to decode some data, an application uses two schemas: the writer's schema, which is identical to the one used for encoding, and the reader's schema, which may be different[^2][^3]. Then, if they are different, Avro resolves the differences by comparing the two and translating the data from the writer's schema into the reader's schema[^4].

**The Key Advantage You Mentioned:**
You're spot on about dynamically generated schemas! One advantage of Avro's approach, compared to Protocol Buffers, is that the schema doesn't contain any tag numbers. The difference is that Avro is friendlier to dynamically generated schemas[^5][^6]. 

The book gives a great example: say you have a relational database whose contents you want to dump to a file. If you use Avro, you can fairly easily generate an Avro schema (in the JSON representation we saw earlier) from the relational schema and encode the database contents using that schema[^7]. By contrast, if you were using Protocol Buffers for this purpose, the field tags would likely have to be assigned by hand. Every time the database schema changed, an administrator would have to manually update the mapping from database column names to field tags[^8].

Excellent understanding of this important distinction!

---

**Question 5:**

What are the main problems with using language-specific encoding formats (like Java's Serializable or Python's pickle) for long-term data storage or communication between services?

```
The major drawback of using language specific encoding is that the encoded object can only be read/decoded by the same language library. This is a limitation the disallow diverse services from communication and for long term data storage it hindrance the application evolvability.
```

Excellent answer! You've identified the most fundamental problem. Let me expand on this with the additional issues the book highlights:

**Language Lock-in (which you correctly identified):**
The encoding is often tied to a particular programming language, and reading the data in another language is difficult. If you store or transmit data in such an encoding, you are committing yourself to your current programming language for potentially a long time and precluding integrating your systems with those of other organizations (which may use different languages)[^1].

**Security Problems:**
To restore data in the same object types, the decoding process needs to be able to instantiate arbitrary classes. This is frequently a source of security problems; if an attacker can get your application to decode an arbitrary byte sequence, they can instantiate arbitrary classes, which in turn often allows them to do terrible things such as remotely executing arbitrary code[^2].

**Poor Versioning Support:**
Versioning data is often an afterthought in these libraries. As they are intended for quick and easy encoding of data, they often neglect the inconvenient problems of forward and backward compatibility[^3]. This directly relates to the evolvability issue you mentioned!

**Efficiency Issues:**
Efficiency (CPU time taken to encode or decode, and the size of the encoded structure) is also often an afterthought. For example, Java's built-in serialization is notorious for its bad performance and bloated encoding[^4].

The conclusion: it's generally a bad idea to use your language's built-in encoding for anything other than very transient purposes[^5].

Great job identifying the core issue!

---

**Question 6:**

In the context of RPC and web services, why can you make the simplifying assumption that servers will be updated first and clients second? What does this mean for the compatibility requirements?

```
In the context of RPC and web services, it is assumed that client code is separate for the server code therefore upgrading server code should happen before updating client code. This lead us to supporting backward compatibility for the application which is easier to achieve. And forward compatibility on responses.
```

Good reasoning! You've got part of it right, but let me clarify and complete the picture:

**The Deployment Reality:**
You're correct that in practice, servers are typically updated first. The book explains: Compared to data flowing through databases, we can make a simplifying assumption in the case of dataflow through services: it is reasonable to assume that all the servers will be updated first and all the clients second[^1].

This happens because server deployments are often controlled by a single organization and can be updated through rolling upgrades, whereas clients (especially mobile apps or desktop software) may be running on user devices and updated at the users' discretion.

**But What Does This Mean for Compatibility?**
Here's the key insight: Thus, you need backward compatibility only on requests, and forward compatibility on responses[^2].

Let me break this down:
- **Backward compatibility on requests**: New servers must be able to read requests from old clients
- **Forward compatibility on responses**: Old clients must be able to read responses from new servers

This is different from databases, where you need both types of compatibility in both directions because old and new code might be reading and writing simultaneously.

Also, note that backward compatibility is normally not hard to achieve. As the author of the newer code, you know the format of data written by older code, so you can explicitly handle it[^3].

So the simplifying assumption helps narrow down which compatibility scenarios you need to worry about!

Does this clarify the relationship between deployment order and compatibility requirements?

---

**Great job working through these questions!** You've demonstrated a solid understanding of encoding and evolution concepts. You correctly identified key ideas around compatibility, binary encoding advantages, and the trade-offs between different formats.

Would you like to move on to the next chapter, or would you like to explore any of these topics in more depth?