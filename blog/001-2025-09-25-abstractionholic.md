# Abstractionholics Anonymous

Hello, everyone, my name is Theodore, and I'm an Abstractionholic.

> The first step to recovery is admitting that one has a problem. Admitting that one is powerless over their desire
to abstract. Admitting that one's usage of abstraction has made their life unmanageable.

I'm here to admit that I have a problem. I am powerless over my compulsion toward abstraction.

## The Dream Stream

Let's take a step back. I think event streaming (à la Apache Kafka) is great. When a problem can be decomposed into a pipeline
of simple operations, then one can handle massive throughput with relatively simple components connected by partitioned and
ordered event streams. This design allows the components in the pipeline to add batching, caching and maintain idempotency with
relatively little effort. However, I've yet to find a system which provides all of the following:

* User-defined partitioning.
* Within a partition, in-order delivery.
* Throughput up to millions of requests per second.
* **Correctness-preserving horizontal scaling**.

Scaling a topic by adding partitions is commonly supported; however, applications which rely on user-defined
partitioning and are sensitive to event order may struggle to gracefully handle events moving between partitions.

Teams have a few options to handle this, none of which seem ideal:

* Over provision partitions to avoid needing to perform scaling. Underutilize these resources indefinitely.
    - If you're trying to empty out an uncomfortably full bank account, this is a great option for you.
* Remove user-defined partitioning, rely on external systems to manage operations.
    - In-memory caching, batching and idempotency become difficult or impossible. Concurrent consumers must coordinate
    transactions, and this contention is expensive to scale.
* Build a stream management layer to handle upgrades.
    - I've not seen this done in practice, but imagine it to be the most practical solve this problem. However, it
    comes at increased development effort and operational complexity.

My "Dream Stream" is a system which manages event partitioning so that scaling is completely opaque to consumers. And I
think I have some good ideas for how that might work. So for the past few months, I've been trying to put my money where
my mouth is.

## Realization

Look, I've only been a software developer for seven years. I've had the fortunate opportunity to build some
pretty neat things, but I have a **lot** left to learn. My recent venture into building the [Dream Stream][DREAM-STREAM]
has made clear to me an unhealthy relationship with Abstraction.

The first meaningful commit was [add the initial hosting abstractions][FIRST-COMMIT]! Wow!
"It's poor design for an application to know about the source of configuration," I told myself! "An application
shouldn't have to know that it is using a UDP socket to talk to another node, it should be abstracted!"
After all, you never know how much time you can save if you decide to switch to something else later! Wow.

Embarassingly, these hosting abstractions consumed my focus for the first week or so of development on the project.

Eventually, I moved on to add implementations of [Interval Tree Clocks][ITC] and the [SWIM Protocol][SWIM] with the
impression that I would need these components to build the Dream Stream.

![An animated video of the SWIM protocol simulator in the abandoned sackosoft/frieze project. A user interacts with buttons to add nodes to a circle representing a cluster. Each node that joins contacts a seed node to join the cluster. The nodes then gossip information between themselves as they run a failure detection protocol based on pinging each other. The user clicks a button to stop a node, within a few seconds some nodes learn of the node failure due to failed ping requests, they gossip this information to other nodes, and each node eventually drops the information for the stopped node.][SWIM-DEMO]

While these were great learning experiences, they make it so painfully obvious that I only have one 'tool in the
toolbox', so to say. Every step along the way I was drawing boxes and arrows; pondering metaphyical code responsibilities
coming up with ways to split things into neat little bundles. It works. It gets stuff done. I get paid big bucks to do
it. But, man, I can't help but feel that every box creates friction. Every every bundle imposes constraints. Every
decision made to offer myself flexibility for a future decision may actually defer the benefit of making a decision at
all.

I can't help but feel there must be a better way. I have a problem. I'm an Abstractionholic.

[DREAM-STREAM]: https://github.com/sackosoft/frieze
[FIRST-COMMIT]: https://github.com/sackosoft/frieze/commit/f0a4ecb394f8438a23533cec06e4637b975d6ec3
[ITC]: https://ferd.ca/interval-tree-clocks.html
[SWIM]: https://en.wikipedia.org/wiki/SWIM_Protocol
[SWIM-DEMO]: res/swim-demo.gif

