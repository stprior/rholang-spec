****************
Syntactic Sugar
****************

Replication
============

Notice that when the reduction rules of the semantics section are used,
the process
::

 x!( for(y <- x){ x!(\*y) \| \*y } \| P )
   \| for(y <- x){ x!(\*y) \| \*y }

reduces to
::
 P \| P \| …

In other words, this expression constitutes an implementation of
replication, and thus the code context for P represents a concurrent
version of the famous Y-combinator for the lambda-calculus.

It is well known that recursion and replication are inter-definable, and
providing a version of recursion from replication is a useful and
instructive exercise. In this connection, notice that it is always
possible to convert an input guarded process expression into one that
will receive its continuation from an outside source. That is,
::

 for(y <- x)P = k!( P ) \| for(y <- x; p <- k)\*k

This observation gives rise to a conversion of processes to a normal
form we call continuation-saturated. Continuation-saturation has many
uses, both in terms of storing processes for long term execution, but
also in terms of supporting reversible computation.
Continuation-saturation is the key to a compilation strategy that turns
every Rholang process into a reversible computation. This affords
natural semantics for transactions with rollback. For purposes of this
discussion, however, note that by factoring processes into their
continuation-saturated normal form it is possible to define a version of
replication that only works for input-guarded processes. For the
ambitious student of the language, it is useful and enlightening to work
out this version of replication.

Because both ! and \* have special significance in the Rholang syntax we
will write $P for replication in the sequel.

Persistent I/O
===============

When sending data over channels, the default behavior is that both the
data and the consumer’s continuation are ephemeral, i.e. that the data
disappears from the channel after being read, and the consumer of the
data reads from the channel exactly once. This is the behavior that
we’ve seen so far, and exactly the same as in the underlying calculus.
However, we also provide syntactic sugar for when the data,
continuation, or both, are persistent, which gives rise to quite
interesting and practical patterns that allow channels to emulate, e.g.
data streams and memory locations. This section describes the different
combinations and explains how they are desugared to the default
behavior. In the interest of clarity, we consider all pairs of the form:

**data consumer** \| *data producer*

Data ephemeral, continuation ephemeral
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is the standard process calculus expression where both the
continuation and the data are ephemeral. That is, the channel is used
exactly once by the consumer, and the data is removed from the channel
once it’s read:

**for(v <- channel) P** \| *channel!(v)*

Data persistent, continuation ephemeral
=========================================

This means that the channel is used as a memory location, i.e. the value
will remain in the channel after it has been read. Traditionally, this
is decided by the producer of the data, by sending it using two
exclamation marks instead of one, like so:

**for(v <- channel) P** \| *channel!!(v)*

The expression above is sugar for

**for(v <- channel) P** \| *$(channel!(v))*

However, sometimes it is useful for the consumer to only peek at the
value in a channel, even if the producer didn’t specify that it should
persist. This is achieved by using the assignment operator :=, as here:

**for(v := channel) P** \| *channel!(v)*

This is interpreted as the consumer simply removing the value and then
resending it, i.e. it is syntactic sugar for:

**for(v <- channel) (channel!(v) \| P)** \| *channel!(v)*

Of course, these two can be combined. Both the producer and the consumer
could want to make sure that the value will stay in the channel, which
they express by:

**for(v := channel) P** \| *channel!!(v)*

Desugaring this using the rules given in the previous two examples, we
see that this is sugar for:

**for(v <- channel) (channel!(v) \| *P)** \| *$(channel!(v))*

Data ephemeral, continuation persistent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the consumer wants to listen to a channel as a stream, it wants to
execute the same continuation for every message that is received. In
other words, the continuation should persist. We signify this using the
stream operator <<, as shown below:

**for(v << channel) P** \| *channel!(v)*

Again writing $P for replication, this is sugar for:

**$(for(v <- channel) P)** \| *channel!(v)*

Data persistent, continuation persistent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Finally, we come to the case where both the data and the continuation
should persist, i.e.:

**for(v << channel) P** \| *channel!!(v)*

There are two natural interpretations of this expression. One is that it
corresponds to an infinite loop and should be caught by the compiler. To
see this interpretation more clearly, we simply desugar using the rules
given above, and get:

**$(for(v <- channel) P)** \| *$(channel!(v))*

That is, the same value should be sent over the channel infinitely many
times, and it should be read and passed to the continuation infinitely
many times.

The other interpretation is that this corresponds to a data stream with
persistent history. This seems like a more useful interpretation to us.
One possible desugaring would be

**$(for(v <- channel) { P \| h(channel)!(v) })** \| *channel!(v))*

Combining persistence choices
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

An interesting case arises when we attempt to join a stream with a
one-off channel, as here:
::

 for(v << state; t <- channel; if cond(v,t)) P
   | state!(v)
   | channel!(t)

There are two natural interpretations that immediately spring to mind.
One is that the one-off channel effectively turns the stream into a
one-off, i.e. it would be semantically equivalent to
::

 for(v <- state; t <- channel; if cond(v,t)) P
  | state!(v)
  | channel!(t)

The other interpretation is that the stream effectively turns the
one-off channel into a stream, by replicating the first value that
appears on channel. In other words, it would be interpreted as
semantically equivalent to
::

 for(v << state; t := channel; if cond(v,t)) P
  | state!(v)
  | channel!(t)

It is interesting to note that under this interpretation, the latter
interpretation would very easily give rise to the following race
condition:
::

 for(v << state; t << channel; if cond(v,t)) P
  | state!(v)
  | channel!(t)
  | channel!(t’)

Here, one of t and t’ will be duplicated, because of the interpretation
of := as reproduction, as it was given above. This gives slight to the
former interpretation, which we have chosen. Of course, it is still
possible to write the race manually:
::

 for(v << state; t := channel) P
  | state!(v)
  | channel!(t)
  | channel!(t’)

In this case, either the programmer knows what she is doing, or it will
be caught by a type judgment.

One guiding principle that informs this work is that parallel
composition represents not just computational autonomy but potentially
autonomy of the authorship of the computation. Thus, for example, in P
\| Q, P could have been written and deployed by organization A, while Q
is written and deployed by organization B. As a result, P cannot make
certain kinds of semantic demands on Q. Thus, in the the expression (
for(v <- state) P ) \| Q , the for-comprehension cannot directly
influence what kind of expression shows up in Q as it may have been
written by a different team at a different time. However, using
rely-guarantee style in the behavioral types, the for-comprehension
could be typed in such a way that the requirements on how Q uses state
could be expressed.

When we consider persisted continuations, aka streams, we will want to
make sure that interleaving of streams in a conditioned join is fair, in
the sense that if computations of values coming out of streams results
in divergence that we allow the programmer to express the desired
policy. For this we will use the `*LogicT semantics devised by Oleg
Kiselyov* <http://okmij.org/ftp/papers/LogicT.pdf>`__.

