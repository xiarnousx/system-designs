**Question 1:**

What is the main difference between declarative and imperative query languages? Provide an example of a declarative query language.

Declarative query languages communicate intent rather than how, on the other hand imperative query languages deal with execution steps specifics. When it comes to declarative languages SQL and Cypher are couple of examples. For imperative I think xPath and xQuery when quering xml document and json document

Good start! You've got the core concept right about declarative vs. imperative—declarative focuses on what you want, while imperative focuses on how to get it.

However, there's a small mix-up in your examples. Let me clarify:

SQL, Cypher, SPARQL, and Datalog are indeed declarative query languages. In declarative languages, you specify the pattern of data you want and what conditions the results must meet, but not how to achieve that goal. In contrast, with most programming languages (such as Python and Java), you would have to write an algorithm telling the computer which operations to perform in which order.

About XPath and XQuery: XQuery and XPath are actually designed to allow complex queries, including joins across multiple documents, and they're also declarative in nature—you describe what you want from the XML document rather than the step-by-step process.

Imperative examples would be more like writing loops and conditionals in languages like Python, Java, or JavaScript to manually navigate through data structures and build your result set step by step.

A key advantage of declarative query languages is that they are typically more concise and easier to write than explicit algorithms. More importantly, they hide implementation details of the query engine, which makes it possible for the database system to introduce performance improvements without requiring any changes to queries.

---

Good start! You've got the core concept right about declarative vs. imperative—declarative focuses on **what** you want, while imperative focuses on **how** to get it.

However, there's a small mix-up in your examples. Let me clarify:

SQL, Cypher, SPARQL, and Datalog are indeed **declarative** query languages. In declarative languages, you specify the pattern of data you want and what conditions the results must meet, but not how to achieve that goal. In contrast, with most programming languages (such as Python and Java), you would have to write an algorithm telling the computer which operations to perform in which order.[^1][^2]

**About XPath and XQuery:** XQuery and XPath are actually designed to allow complex queries, including joins across multiple documents[^3], and they're also **declarative** in nature—you describe what you want from the XML document rather than the step-by-step process.

**Imperative examples** would be more like writing loops and conditionals in languages like Python, Java, or JavaScript to manually navigate through data structures and build your result set step by step.

A key advantage of declarative query languages is that they are typically more concise and easier to write than explicit algorithms. More importantly, they hide implementation details of the query engine, which makes it possible for the database system to introduce performance improvements without requiring any changes to queries.[^4]

---

**Question 2:**

Explain the concept of 'impedance mismatch' in the context of object-oriented programming and relational databases.

Impedance mismatch is also referred to as object mismatch. The idea is that data stored in rows and columns such as relational databases often mismatch application objects modeling. ORMs are used to close this gap.

Excellent! You've captured the essence of impedance mismatch very well.

To expand on your answer: Much application development today is done in object-oriented programming languages, which leads to a common criticism of the SQL data model: if data is stored in relational tables, an awkward translation layer is required between the objects in the application code and the database model of tables, rows, and columns. The disconnect between the models is sometimes called an impedance mismatch.

You're also correct about ORMs! Object-relational mapping (ORM) frameworks like ActiveRecord and Hibernate reduce the amount of boilerplate code required for this translation layer, but they are often criticized.

However, it's worth noting that ORMs are complex and can't completely hide the differences between the two models, so developers still end up having to think about both the relational and the object representations of the data.

Great job on this one!

---

Excellent! You've captured the essence of impedance mismatch very well.

To expand on your answer: Much application development today is done in object-oriented programming languages, which leads to a common criticism of the SQL data model: if data is stored in relational tables, an awkward translation layer is required between the objects in the application code and the database model of tables, rows, and columns. The disconnect between the models is sometimes called an impedance mismatch.[^1]

You're also correct about ORMs! Object-relational mapping (ORM) frameworks like ActiveRecord and Hibernate reduce the amount of boilerplate code required for this translation layer, but they are often criticized.[^2]

However, it's worth noting that ORMs are complex and can't completely hide the differences between the two models, so developers still end up having to think about both the relational and the object representations of the data.[^3]

Great job on this one!

---

**Question 3:**

What are the key differences between normalization and denormalization? When would you choose one over the other?

Normalization is a form of not repeating information across multiple tables which solves inconsistencies problem. However normalized data comes at the price of joins as this problem escalates as data volume grows. On the other hand denormalization often used with document embeddings, however it has it cost in data inconsistencies and requires application level work in order to make sure state is updated.

Excellent answer! You've identified the key trade-offs very well. Let me reinforce and expand on your points:

On normalization: When you use an ID, your data is more normalized: the information that is meaningful to humans (such as the text Washington, DC) is stored in only one place, and everything that refers to it uses an ID (which has meaning only within the database). When you store the text directly, you are duplicating the human-meaningful information in every record that uses it; this representation is denormalized.

On the performance trade-offs: As a general principle, normalized data is usually faster to write (since there is only one copy) but slower to query (since it requires joins); denormalized data is usually faster to read (fewer joins) but more expensive to write (more copies to update, more disk space used).

When to choose each approach: You nailed the key consideration about data volume! Normalization tends to be better for OLTP systems, where both reads and updates need to be fast; analytical systems often fare better with denormalized data, since they perform updates in bulk and the performance of read-only queries is the dominant concern. In systems of small to moderate scale, a normalized data model is often best because you don't have to worry about keeping multiple copies of the data consistent with one another, and the cost of performing joins is acceptable. However, in very large-scale systems, the cost of joins can become problematic.

Great understanding of this important concept!

---

Excellent answer! You've identified the key trade-offs very well. Let me reinforce and expand on your points:

**On normalization:** When you use an ID, your data is more normalized: the information that is meaningful to humans (such as the text Washington, DC) is stored in only one place, and everything that refers to it uses an ID (which has meaning only within the database). When you store the text directly, you are duplicating the human-meaningful information in every record that uses it; this representation is denormalized.[^1]

**On the performance trade-offs:** As a general principle, normalized data is usually faster to write (since there is only one copy) but slower to query (since it requires joins); denormalized data is usually faster to read (fewer joins) but more expensive to write (more copies to update, more disk space used).[^2]

**When to choose each approach:** You nailed the key consideration about data volume! Normalization tends to be better for OLTP systems, where both reads and updates need to be fast; analytical systems often fare better with denormalized data, since they perform updates in bulk and the performance of read-only queries is the dominant concern. In systems of small to moderate scale, a normalized data model is often best because you don't have to worry about keeping multiple copies of the data consistent with one another, and the cost of performing joins is acceptable. However, in very large-scale systems, the cost of joins can become problematic.[^3]

Great understanding of this important concept!

---

**Question 4:**

Describe the property graph model. What are its main components and how do they relate to each other?

Graph models represents entities as vertices  and edges. Edges represents relationship between vertices. On the other hands vertices represents objects to model such as a person. Both edges and vertices can have properties and metadata. Vertices can also be pointed toward through edges by one or more vertices.

Excellent! You've got the fundamental concepts of the property graph model down very well. Let me add some more specific details from the chapter:

On vertices: In the property graph (also known as labeled property graph) model, each vertex consists of a unique identifier, a collection of properties (key-value pairs), and a label (string) to describe the type of object this vertex represents.

On edges: Each edge consists of the vertex at which the edge starts (the tail vertex), the vertex at which the edge ends (the head vertex), a label to describe the kind of relationship between the two vertices, and a collection of properties (key-value pairs).

Key characteristics you mentioned are spot-on! A few additional important aspects:

Any vertex can have an edge connecting it with any other vertex. There is no schema that restricts which kinds of things can or cannot be associated.

Given any vertex, you can efficiently find both its incoming and its outgoing edges and thus traverse the graph (i.e., follow a path through a chain of vertices) both forward and backward.

By using different labels for different kinds of vertices and relationships, you can store several kinds of information in a single graph, while still maintaining a clean data model.

Great job!

---

Excellent! You've got the fundamental concepts of the property graph model down very well. Let me add some more specific details from the chapter:

**On vertices:** In the property graph (also known as labeled property graph) model, each vertex consists of a unique identifier, a collection of properties (key-value pairs), and a label (string) to describe the type of object this vertex represents.[^1][^2]

**On edges:** Each edge consists of the vertex at which the edge starts (the tail vertex), the vertex at which the edge ends (the head vertex), a label to describe the kind of relationship between the two vertices, and a collection of properties (key-value pairs).[^3][^4][^5]

**Key characteristics you mentioned are spot-on!** A few additional important aspects:

Any vertex can have an edge connecting it with any other vertex. There is no schema that restricts which kinds of things can or cannot be associated.[^6]

Given any vertex, you can efficiently find both its incoming and its outgoing edges and thus traverse the graph (i.e., follow a path through a chain of vertices) both forward and backward.[^7]

By using different labels for different kinds of vertices and relationships, you can store several kinds of information in a single graph, while still maintaining a clean data model.[^8]

Great job!

---

**Question 5:**

What is event sourcing and what are its main advantages? What is one challenge or limitation of using event sourcing?

Event sourcing is an append only log that captures events happening on the object/entity life cycle. The advantages of event sourcing the fast writes and history replay. Few disadvantages comes with event sourcing comes from distributed systems and referencing external data that may change.

Excellent answer! You've captured some of the core concepts and trade-offs very well. Let me expand on your points:

Definition: The idea of using events as the source of truth and expressing every state change as an event is known as event sourcing. Perhaps the simplest, fastest, and most expressive way of writing data is an event log: every time you want to write some data, you encode it as a self-contained string (perhaps as JSON), including a timestamp, and then append it to a sequence of events. Events in this log are immutable; you never change or delete them, but only ever append more events to the log (which may supersede earlier events).

You're absolutely right about fast writes and history! Event logs can typically handle higher write throughput than databases because of their sequential access patterns. If you have a temporary burst of events, the log can absorb it, and downstream systems that maintain materialized views can catch up at their own pace without becoming overwhelmed.

And on reproducibility: A key principle of event sourcing is that the materialized views are derived from the event log in a reproducible way. You should always be able to delete the materialized views and recompute them by processing the same events in the same order, using the same code.

On the challenge with external data: Spot on! You need to be careful if external information is involved. For example, say an event contains a price given in one currency, and for one of the views it needs to be converted into another currency. Since the exchange rate may fluctuate, it would be problematic to fetch the exchange rate from an external source when processing the event, since you would get a different result if you recomputed the materialized view on another date. To make the event processing logic deterministic, you must either include the exchange rate in the event itself or have a way of querying the historical exchange rate at the timestamp indicated in the event.

On distributed systems: The only important requirement is that the event storage system must guarantee that all materialized views process the events in exactly the same order as they appear in the log. As we shall see in Chapter 10, this is not always easy to achieve in a distributed system.

Great understanding!


---

Excellent answer! You've captured some of the core concepts and trade-offs very well. Let me expand on your points:

**Definition:** The idea of using events as the source of truth and expressing every state change as an event is known as event sourcing.[^1] Perhaps the simplest, fastest, and most expressive way of writing data is an event log: every time you want to write some data, you encode it as a self-contained string (perhaps as JSON), including a timestamp, and then append it to a sequence of events. Events in this log are immutable; you never change or delete them, but only ever append more events to the log (which may supersede earlier events).[^2]

**You're absolutely right about fast writes and history!** Event logs can typically handle higher write throughput than databases because of their sequential access patterns. If you have a temporary burst of events, the log can absorb it, and downstream systems that maintain materialized views can catch up at their own pace without becoming overwhelmed.[^3]

And on reproducibility: A key principle of event sourcing is that the materialized views are derived from the event log in a reproducible way. You should always be able to delete the materialized views and recompute them by processing the same events in the same order, using the same code.[^4]

**On the challenge with external data:** Spot on! You need to be careful if external information is involved. For example, say an event contains a price given in one currency, and for one of the views it needs to be converted into another currency. Since the exchange rate may fluctuate, it would be problematic to fetch the exchange rate from an external source when processing the event, since you would get a different result if you recomputed the materialized view on another date. To make the event processing logic deterministic, you must either include the exchange rate in the event itself or have a way of querying the historical exchange rate at the timestamp indicated in the event.[^5]

**On distributed systems:** The only important requirement is that the event storage system must guarantee that all materialized views process the events in exactly the same order as they appear in the log. As we shall see in Chapter 10, this is not always easy to achieve in a distributed system.[^6]

Great understanding!

---

**Question 6:**

Compare and contrast the document model and the relational model. In what scenarios would you choose the document model over the relational model?


Document model is schema on read while relational model is schema on write. Both have advantages and disadvantages as with everything else it is a matter of tradeoffs. Modern relational systems support xml and json data types which bring the best of the two worlds. I would use document model if for example I am building a todo list, resume builder kind of application. Whereas I would use relational model if I want to have data consistency in my application.

Excellent answer! You've identified several key points accurately. Let me reinforce and expand on what you said:

On schema flexibility: Document databases are sometimes called schemaless, but that's misleading as the code that reads the data usually assumes some kind of structure—that is, there is an implicit schema, but it is not enforced by the database. A more accurate term is schema-on-read (the structure of the data is implicit and interpreted only when the data is read), in contrast with schema-on-write (the traditional approach of relational databases, where the schema is explicit and the database ensures that all data conforms to it when the data is written).

On convergence: You're absolutely right about modern systems! Document databases and relational databases started out as very different approaches to data management, but they have grown more similar over time. Relational databases added support for JSON types and query operators, and the ability to index properties inside documents. Some document databases (such as MongoDB, Couchbase, and RethinkDB) added support for joins, secondary indexes, and declarative query languages. This convergence of the models is good news for application developers, because the relational model and the document model work best when you can combine both in the same database.

When to use document model: Great examples! If the data in your application has a document-like structure (i.e., a tree of one-to-many relationships, where typically the entire tree is loaded at once), then it's probably a good idea to use a document model. The relational technique of shredding—splitting a document-like structure into multiple tables (like positions, education, and contact_info in Figure 3-1)—can lead to cumbersome schemas and unnecessarily complicated application code.

Main arguments for each model: The main arguments in favor of the document data model are schema flexibility, better performance due to locality, and that for some applications it is closer to the object model used by the application. The relational model counters by providing better support for joins and many-to-one and many-to-many relationships.

---

Fantastic work! You've demonstrated a strong understanding of all the major concepts in this chapter. Great job working through these questions! Would you like to move on to the next chapter, or would you like to explore any of these topics in more depth?