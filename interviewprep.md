# interview prep resources 

### Evan King - Linkedin
Your system design textbooks are outdated, and it's hurting your interview performance.

Here's what I keep seeing in system design interviews: candidates doing scale calculations with numbers from 2020 (or even 2015!). They're worried about database sizes and memory constraints that haven't been relevant for years.

It's not their fault – they're studying the right materials. But hardware capabilities have exploded, and most resources haven't kept up.

Here's what actually matters in 2025:

- A single DB instance can handle 64TB+ with sub-10ms latency. Yet most candidates jump to sharding immediately.
- Modern caches routinely handle terabyte-scale datasets. All that "careful memory management" advice is outdated.
- Message queues like Kafka process millions of messages per second with single-digit millisecond latency. They're not just for async anymore.

The implications are huge. Systems that required complex distributed architectures five years ago can now run on a single machine. Most distributed databases being designed in interviews are solutions to problems that don't exist.

My point isn't that vertical scaling is the answer to everything. The world's largest systems still need distribution. But knowing the real limits helps you avoid premature optimization – and that's what experienced interviewers actually look for in interviews.

Read more about the latest numbers to know in 2025 in our latest newsletter!

https://hellointerview.substack.com/p/modern-hardware-numbers-for-system
