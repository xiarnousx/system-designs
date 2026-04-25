Great! Let's start with some questions to test your understanding of Chapter 2: Defining Nonfunctional Requirements:

**Question 1:**

What is the difference between functional and nonfunctional requirements? Give an example of each.

Excellent answer! You've captured the key distinction perfectly.

Functional requirements are indeed the specifics like what screens and buttons you need, and what each operation is supposed to do to fulfill the purpose of your software. Your shopping cart example is spot-on for an ecommerce platform.

And you're correct that nonfunctional requirements include things like the app being fast, reliable, secure, legally compliant, and easy to maintain. These requirements might not be explicitly written down because they may seem somewhat obvious, but they are just as important as the app's functionality—an app that is unbearably slow or unreliable might as well not exist.

**Excellent answer!** You've captured the key distinction perfectly. 

Functional requirements are indeed the specifics like what screens and buttons you need, and what each operation is supposed to do to fulfill the purpose of your software.[^1] Your shopping cart example is spot-on for an ecommerce platform.

And you're correct that nonfunctional requirements include things like the app being fast, reliable, secure, legally compliant, and easy to maintain.[^2] These requirements might not be explicitly written down because they may seem somewhat obvious, but they are just as important as the app's functionality—an app that is unbearably slow or unreliable might as well not exist.[^3]

Great start! Let's move on.

---

**Question 2:**

Explain the concept of 'fan-out' in the context of the social network case study and describe how it relates to materialized timelines.


The fan-out is a queue that updates the followers timelines using server side pushes it relates to the materialized timelines which append into it.

Good effort! You're on the right track with the core idea, but let me help clarify a few details.

Fan-out is the term used to describe the factor by which the number of requests increases when one initial request results in several downstream requests being carried out.

In the social network case study: For each user, there's a data structure containing their home timeline (recent posts by people they're following). Every time a user makes a post, the system looks up all their followers and inserts that post into the home timeline of each follower—like delivering a message to a mailbox.

So if a user has 200 followers and makes one post, that single post triggers 200 timeline updates—that's a fan-out factor of 200. At a rate of 5,800 posts per second, if the average post reaches 200 followers (i.e., a fan-out factor of 200), the system needs to do just over 1 million home timeline writes per second.

You were right about the server-side pushes and the relationship to materialized timelines! The key addition is that fan-out specifically refers to that multiplication effect—one action causing many.


**Good effort!** You're on the right track with the core idea, but let me help clarify a few details.

Fan-out is the term used to describe the factor by which the number of requests increases when one initial request results in several downstream requests being carried out.[^1] 

In the social network case study: For each user, there's a data structure containing their home timeline (recent posts by people they're following). Every time a user makes a post, the system looks up all their followers and inserts that post into the home timeline of each follower—like delivering a message to a mailbox.[^2]

So if a user has 200 followers and makes one post, that single post triggers 200 timeline updates—that's a fan-out factor of 200. At a rate of 5,800 posts per second, if the average post reaches 200 followers (i.e., a fan-out factor of 200), the system needs to do just over 1 million home timeline writes per second.[^3]

You were right about the server-side pushes and the relationship to materialized timelines! The key addition is that fan-out specifically refers to that *multiplication effect*—one action causing many.

---

**Question 3:**

What is the difference between response time and latency as defined in this chapter?

Response time is what the client sees, latency is often related to network latency i.e. time for data on wire.

Excellent! You've nailed the key distinction.

The response time is what the client sees; it includes all delays incurred anywhere in the system.

And you're absolutely right about latency: Latency is a catchall term for time during which a request is not being actively processed—that is, during which it is latent. In particular, network latency or network delay refers to the time that a request and response spend traveling through the network.

So response time is the total time from the client's perspective (including network delays, queueing, and actual processing), while latency refers to the "waiting" time when nothing is actively being processed—most commonly the time spent in transit over the network.

Perfect understanding!


**Excellent!** You've nailed the key distinction.

The response time is what the client sees; it includes all delays incurred anywhere in the system.[^1]

And you're absolutely right about latency: Latency is a catchall term for time during which a request is not being actively processed—that is, during which it is latent. In particular, network latency or network delay refers to the time that a request and response spend traveling through the network.[^2]

So response time is the *total* time from the client's perspective (including network delays, queueing, and actual processing), while latency refers to the "waiting" time when nothing is actively being processed—most commonly the time spent in transit over the network.

Perfect understanding!

---

**Question 4:**

Why is the median (p50) often a better metric than the mean for describing typical response times?

The median p50 often better than mean because it eliminates outliers.

Good thinking! You're on the right track about outliers, but let me refine that a bit.

The median doesn't exactly eliminate outliers—those slow requests still exist and still affect users. Rather, the mean is not a very good metric if you want to know your "typical" response time, because it doesn't tell you how many users actually experienced that delay.

Here's why the median is better: If you take your list of response times and sort it from fastest to slowest, the median is the halfway point—for example, if your median response time is 200 ms, that means half your requests return in less than 200 milliseconds, and half your requests take longer. This makes the median a good metric if you want to know how long users typically have to wait.

The mean can be skewed by a few very slow outliers, giving you a number that most users didn't actually experience. The median tells you what the typical user experiences—literally the middle of the distribution.

Great progress!


**Good thinking!** You're on the right track about outliers, but let me refine that a bit.

The median doesn't exactly *eliminate* outliers—those slow requests still exist and still affect users. Rather, the mean is not a very good metric if you want to know your "typical" response time, because it doesn't tell you how many users actually experienced that delay.[^1]

Here's why the median is better: If you take your list of response times and sort it from fastest to slowest, the median is the halfway point—for example, if your median response time is 200 ms, that means half your requests return in less than 200 milliseconds, and half your requests take longer. This makes the median a good metric if you want to know how long users typically have to wait.[^2]

The mean can be skewed by a few very slow outliers, giving you a number that most users didn't actually experience. The median tells you what the *typical* user experiences—literally the middle of the distribution.

Great progress!

---

**Question 5:**

Explain the distinction between a fault and a failure in the context of system reliability.

A fault may occur to a component, while the complete system sill operates, while failure render the whole system inoperable.

Exactly right! You've captured the essential distinction perfectly.

A fault occurs when a particular part of a system stops working correctly—for example, if a single hard drive malfunctions, or a single machine crashes, or an external service (that the system depends on) has an outage.

A failure occurs when the system as a whole stops providing the required service to the user—in other words, when it does not meet the SLO.

The distinction between faults and failures can be confusing because they are the same thing, just at different levels. For example, if a hard drive stops working, we say that the hard drive has failed; if the system consists of only that one hard drive, it has stopped providing the required service and thus has also failed. However, if the system consists of multiple hard drives, the failure of a single hard drive is only a fault from the point of view of the bigger system, and the bigger system might be able to tolerate that fault by having a copy of the data on another hard drive.

So a fault-tolerant system can continue operating even when faults occur, preventing them from escalating into failures. Excellent understanding!

**Exactly right!** You've captured the essential distinction perfectly.

A fault occurs when a particular part of a system stops working correctly—for example, if a single hard drive malfunctions, or a single machine crashes, or an external service (that the system depends on) has an outage.[^1]

A failure occurs when the system as a whole stops providing the required service to the user—in other words, when it does not meet the SLO.[^2]

The distinction between faults and failures can be confusing because they are the same thing, just at different levels. For example, if a hard drive stops working, we say that the hard drive has failed; if the system consists of only that one hard drive, it has stopped providing the required service and thus has also failed. However, if the system consists of multiple hard drives, the failure of a single hard drive is only a fault from the point of view of the bigger system, and the bigger system might be able to tolerate that fault by having a copy of the data on another hard drive.[^3]

So a fault-tolerant system can continue operating even when faults occur, preventing them from escalating into failures. Excellent understanding!

---

**Question 6:**

What are the three key principles for maintainability mentioned in the chapter, and what does each one focus on?

The three key principles of maintainability are Operability, Simplicity and Evolvability. Operatable it is easy to operate, simplicity it is easy to extend. Evolvability it is easy to update.

**Great job!** You've got all three principles correct, and your descriptions are very close. Let me just refine them slightly:

The three principles are:

**Operability** – Make it easy for the organization to keep the system running smoothly.

**Simplicity** – Make it easy for new engineers to understand the system, by implementing it using well-understood, consistent patterns and structures and avoiding unnecessary complexity.

**Evolvability** – Make it easy for engineers to make changes to the system in the future, adapting it and extending it for unanticipated use cases as requirements change.[^1]

You were spot-on with operability and evolvability! For simplicity, the focus is on making it easy to *understand* the system (which then makes it easier to extend, as you mentioned). The key is reducing complexity so new engineers can grasp how the system works.

---

**Excellent work!** You've worked through all six questions and demonstrated a solid understanding of the key concepts in Chapter 2: Defining Nonfunctional Requirements. You clearly grasp functional vs. nonfunctional requirements, performance metrics, reliability concepts, and maintainability principles.

Would you like to move on to the next chapter, or would you like to explore any of these topics in more depth?