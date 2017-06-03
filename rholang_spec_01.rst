Contracts, Composition, and Scaling

The Rholang 0.1 specification

3 Aug 2016

Lucius Gregory Meredith

Jack Pettersson

Gary Stephenson

Intended audience: Developers using the RChain Platform; developers
creating or maintaining the RChain Platform; language designers;
concurrency theorists; blockchain and cryptocurrency enthusiasts

As playful as the natural world is, natural actions have natural
consequences.

|image0|\ |image1|

The contract between client and service provider

|image2|

|image3|

can mean the difference between flourishing and failing.

|image4|

Yet, with stakes this high nature scales naturally. Gracefully.
Beautifully.

Instinctively, we know how nature does this. Every child can see the
tree in the leaf. Yet, we forget, and we only seem to remember, when we
remember at all, at the crucial moment when we need to build systems at
scale, and the systems we have built are failing. Like now.

`*Introduction and motivation* <#_xxxate8m2jw0>`__

`*The need for social contracts* <#_11pdi8a6a2f3>`__

`*Examples of social contracts* <#_gjet9ber4y85>`__

`*How social contracts differ from Ethereum’s smart
contracts* <#_zhkqeysix92n>`__

`*Language specification* <#_8xmt0rhav6i0>`__

`*Syntax* <#_bue14dmdh4bx>`__

`*RChain’s Social Contract Language* <#_q0db46dr5o8d>`__

    `*Top level declarations and control flow* <#_r0bbgwqdmmtr>`__

    `*Builtin types* <#_e3xr2ic3h534>`__

    `*Pattern matching* <#_f7r8choj6sw5>`__

    `*Behavioral types* <#_n0w7gr3z9ei9>`__

`*Syntactic sugar* <#_7vriwmwc5c21>`__

`*Replication* <#_ndkld7kek2sz>`__

`*Persistent I/O* <#_v6pycodnqvjo>`__

`*Combining persistence choices* <#_30c3ldqqj7po>`__

`*Primitives, builtins, and foreign functions* <#_97wq3fvrkx18>`__

`*An example from arithmetic* <#_x99iaewjwhf>`__

`*Modularity, efficient implementation, and scalable
verification* <#_6mce1bb82606>`__

`*Semantics* <#_1dwll9kegtmz>`__

`*A specification of the rho-calculus with
summation* <#_8g1t14a52iih>`__

`*Syntax* <#_e0lh3g6yxwe3>`__

    `*Free and bound names* <#_58lhlsepdvld>`__

    `*Structural equivalence* <#_af359akea37d>`__

    `*Name equivalence* <#_ph5bu8t85uld>`__

    `*Semantic versus syntactic substitution* <#_9s9501yu8sd>`__

    `*Reduction relation* <#_s2asnmzc8hw3>`__

`*Conclusion* <#_nwewjwplftdy>`__

Introduction and Motivation

The need for social contracts

Bitcoin and its proof-of-work based blockchain pointed the way towards a
trustless medium for financial and information-based exchange by which
this generation can address the egregious failures of the financial
systems and the states that allegedly regulate and govern them. By
eliminating the role of the trusted 3rd party, this technology and the
generation that adopts it has no use for financial institutions that are
too big to fail, nor do they have to kowtow to government agencies that
subsidize systemic irresponsibility with taxpayer funds. They can
realize effective alternatives without having to stomach the absurdity
that there’s no other way, nor wait for public institutions to come up
to speed on the real potential of Internet technologies. However, the
incumbent financial services layer is rich with complex financial
instruments, a fact not lost on Ethereum and others working on emerging
smart contract technologies.

These new offerings enrich the basics of the blockchain tool set with
computationally complete platforms in which to re-engineer, and more
importantly reimagine, the layer of financial instruments. Meanwhile,
efforts like Factom recognize that blockchain isn’t just for keeping
ledgers of who has how many quatloos. Records of land trust are
frequently massively abused in many countries, and Factom is putting
this information on the blockchain so that all can see a public record
and attestation of ownership. [1]_

Likewise, RChain recognizes that information flow goes hand in hand with
financial instrumentation. Further, it’s not just about provenance.
Contractual obligations in the blockchain enabled world are just as much
about the when, where, and how of disclosure of sensitive information.
RChain introduces these ideas in a relatively low-risk setting of social
networks, but this is primarily because we believe that establishing
trust in the technology in low-risk settings, at Internet-wide scale is
the right path to adoption in markets where there is more risk, such as
freelance work, or dating, the exchange of medical records, or
self-determination via the Internet.

Further, RChain recognizes that a computationally complete platform
needs two key elements to provide realistic and scalable services. First
and foremost it needs a clear computational semantics grounded in formal
methods. ML and Haskell are two prominent and popular languages enjoying
this kind of grounding and their success is in no small measure derived
from their foundations. Of course, these languages were conceived when
computing was largely a desktop phenomenon, not the fabric of global
society, taking place on billions of interconnected devices, with a
communication topology that is continually dynamically reconfiguring. As
such, neither language, nor the computational models on which they are
based are particularly well suited for this new purpose. No, the
computational semantics demanded must be one that suits the requirements
of this time and place -- which is why RChain’s contract language is
based on the mobile process calculi, which provide, by design, a
mathematical model of today’s computing infrastructure.

Secondly, but of equal weight, the computational platform must provide a
means to constrain, probe, and examine contracts, which became
especially apparent in the wake of the exploit of “The DAO”. The
accepted way to do this in modern programming language design and
programming language semantics is with types. RChain’s contract language
comes equipped with a modern, behavioral typing system. This system
allows parties seeking to engage contractually with a means to probe the
contractual obligations and guarantees in an automated way. This gives
potential participants a chance to determine if becoming party to a
particular contract is really the right choice.

Additionally, when a new technology makes it easy to forge or
manufacture a useful artifact, what follows shortly after is a
proliferation of such artifacts. Making it easy to create and execute
smart contracts will invariably result in an abundance of smart
contracts. Think about this for a minute. Smart contract is just another
name for code. If we’ve learned nothing else from source control systems
from CVS to SVN to github, we should have learned that code is the dark
matter of the Internet. Whether inside the corporate firewall or outside
in the land of open source, searching for code that does just what needs
to be done is a dark art, best left to wizards. Already, technologies
like Hoogle are aimed at searching on the basis of type information.
RChain’s behavioral types makes it possible to take that idea much, much
further -- to search code on the basis of how it is shaped and what it
does -- not just what strings, keywords or phrases might happen to show
up somewhere in its text.

Both of these features address scaling in several dimensions. Choosing a
semantics that naturally reflects how computing actually happens in the
Internet is of paramount importance. Ethereum’s choice of VM that
imposes global serialization across Internet scale contracts is
precisely why Ethereum V 1.0 won’t scale. Well, that and the fact that
proof-of-work doesn’t scale, either. At DevCon1, Vitalik’s talk on
scaling Ethereum admits just this. Both his proposals for changing the
architecture and for the corresponding changes to the contract language
that codes to it are several steps in the direction of the model RChain
has had from the beginning.

Where RChain’s choices really begin to shine, however, are in the
features that emerge from having a behavioral type system. One of the
key aspects of scaling is not just how fast or how many, but who and
why. Which agents will use the system and for what purpose? It won’t be
long before the major corporate agencies realize that blockchain -- as
currently rendered in running code -- is simply a much, much slower way
to do things than their internal systems. It has virtually no use behind
the corporate firewall. It’s real value is in the public domain between
private agents. Add to this that the fact that it’s brand new technology
with all manner of warts and risks and interest will wane very soon.
Solving the speed and throughput problems alone won’t address this
latter concern.

Behavioral typing of social contracts means that -- for the first time
-- we have a technology that can enforce information flow and liveness
properties at Internet scale. This is the natural next step in
trustlessness. Agents who never had a relationship can become party to
an automated contract with certainty that that contract will not leak
sensitive information, and that that contract will -- under appropriate
guarantees -- reliably execute mission-critical transactions. These
kinds of guarantees will bring corporate and governmental agency to the
blockchain -- because these agencies are built on providing
mission-critical services. But bringing these players to the table
effectively shifts the balance of power back to the public that
maintains such a valuable infrastructure. That’s scaling of a completely
different color, to mix metaphors.

Examples of social contracts

Let’s bring these ideas back to earth, and begin somewhere small by
making a contract that holds a resource, like a balance, and allows
clients to get and set the value of the resource. Such Cell-like
behavior is the “hello world” of the blockchain.

contract Cell( get, set, state ) = {

 for( rtn <- get; v <- state ) {

 rtn!( v ) \| state!( v ) \| Cell( get, set, state )

 } \|

 for( newValue <- set; v <- state ) {

 state!( newValue ) \| Cell( get, set, state )

 }

}

This takes a channel for get requests, a channel for set requests, and a
state channel where we will hold the resource. In parallel it waits on
the get and set channels for client requests. It joins an incoming
client with a request against the state channel. This join does two
things. Firstly, it removes the internal state from access while this,
in turn, serializes get and set actions, so that they are always
operating against a single consistent copy of the resource. Of course,
the serialization is still subject to the non-deterministic arrival
order of client requests. But, there will be some serialization of those
requests providing a single history of accesses and updates against the
state.

A get request comes with a rtn channel where the value in state will be
sent. Since the value has been taken from the state channel, it is put
back, and the Cell behavior is recursively invoked. A set request comes
with a new value, which is published to the state channel (the old value
having been stolen by the join). Meanwhile, the Cell behavior is
recursively invoked.

We can instantiate and run the Cell with a private state channel, called
current, an initial value, initial by

new current in Cell( get, set, current ) \| current!( initial )

and we can wrap that up in a Wallet contract that is parametric in the
get and set channels and the initial value.

contract Wallet( get, set, initial ) = {

 new current in Cell( get, set, current ) \| current!( initial )

}

Finally, we can instantiate a Wallet.

Wallet( get, set, 1000.00 )

That’s a simple Wallet contract that can hold a balance (or other kind
of resource), and allow the balance to be updated.

One less desirable aspect of this implementation is that it will
accumulate threads. To see this, consider what happens when servicing a
client’s request on the get channel. The Cell is recursively invoked;
however, there is still a thread waiting to service a set request, and
another such thread will be launched on the recursive call to Cell. A
large imbalance of get (resp. set) requests and this implementation will
run out of memory. A safer implementation would use the select construct

contract Cell( get, set, state ) = {

 select {

 case rtn <- get; v <- state => {

 rtn!( \*v ) \| state!( \*v ) \| Cell( get, set, state )

 }

 case newValue <- set; v <- state => {

 state!( \*newValue ) \| Cell( get, set, state )

 }

 }

}

This implementation may be substituted into the Wallet contract without
any perturbation to that code context. However, when it is run only one
of the threads in Cell can respond to the client request. It’s a race,
and the losing thread, be it getter or setter, is killed. This way, when
the recursive invocation of Cell is called, the losing thread is not
hanging around, yet the new Cell process is still able to respond to
either type of client request.

For programmers who prefer a more object-oriented style with rich
message structure, there is yet a third option that uses only one client
request channel, and dispatches on the type of the message received on
the channel.

contract Cell( client, state ) = {

 for( request <- client; v <- state ) {

match request {

 get{ rtn } => {

 rtn!( v ) \| state!( v ) \| Cell( client, state )

 }

 set{ newValue } => {

 state!( newValue ) \| Cell( client, state )

 }

 }

 }

}

This implementation would require a change to the Wallet contract.
Either the Wallet contract has to turn requests on the get and set
channels into messages

contract Adapter( get, set, client ) = {

 select {

case rtn <- get => {

 client!( get{ rtn } ) \|

 Adapter( get, set, client )

};

case newValue <- set => {

 client!( set{ newValue } ) \|

 Adapter( get, set, client )

}

 }

}

contract Wallet( get, set, initial ) = {

 new client, current in

Adapter( get, set, client ) \|

current!( initial ) \|

Cell( client, current )

}

or it has to pass along to clients the change in the contractual
interface.

contract Wallet( client, initial ) = {

 new current in Cell( client, current ) \| current!( initial )

}

Even with this basic example we can see many of the salient features of
the language. Concurrent execution, asynchronous message-passing, and
pattern matching are woven together into a simple, easy-to-understand
language.

How social contracts differ from Ethereum’s smart contracts

To begin with, Ethereum’s contracts are internally sequential. In fact,
the entire call-chain stemming from a point of entry at a single
contract will have a global serial order. Think about this in terms of
supply chain management. Does Ford Motor Company want to serialize the
tire supply with the chassis or engine or electrical supply? Does Boeing
want to serialize the fuel system supply with the lighting or interior
seating supply? Businesses are made and broken on efficiencies stemming
from being able to manage processes in parallel, and coordinate them
concurrently. Yet, surely Ford and Boeing could greatly benefit from a
smart contract based supply chain management system. Just as with
Haskell or ML, the model chosen doesn’t fit the domain.

In point of fact, an earlier technology, business process modeling,
already explored just this application. Microsoft’s Biztalk, as well as
standards like BPEL, BPML, W3C Choreography, to name a few, all
concluded that concurrency was the currency, so to speak, and opted to
choose the mobile process calculi as their semantic foundation. The
paradigmatic application example in business process modeling is supply
chain management.

Language Specification

Syntax

First we present the basic syntax.

RChain’s Social Contract Language

RChain offers a typed contract language that provides a semantics
naturally suited for decentralized, distributed computing. It is built
around communicating mobile processes.

Top level declarations and control flow

<contract> ::=

 contract <name> ( <ptrn> [: <sort>] [, <ptrn> [: <sort>]]\* ) [:
<type>] = { <process> }

<process> ::= Nil

\| for( <ptrn> <- <channel> [; <ptrn> <- <channel>]\* [; if <cond>] )
<process>

\| select { <branch> [; <branch>]\* }

\| match <process> { <ptrn> => <process> [; <ptrn> => <process>]\* }

\| <channel> ! ( <process> [, <process>]\* )

\| <process> \| <process>

\| \*<channel>

\| new <var> [, <var>]\* in <process>

\| <name> ( <process> [, <process]\* )

\| <value>

<branch> ::= case <ptrn> <- <channel> [; <ptrn> <- <channel>]\* =>
<process>

Builtin types

<channel> ::= <address>

\| <var>

<address> ::= <keccak256>

\| @<process>

<value> ::= <quantity>

\| <entity>

<quantity> ::= <boolean>

\| <integer>

<entity> ::= <char>

\| <datetime>

\| <struct>

\| <collection>

<struct> ::= <name> { <process> [, <process>]\* }

<collection> ::= <string>

\| <array>

\| <list>

Pattern matching

<ptrn> ::= Nil

\| for( <var> [; if <var>] ) <ptrn>

\| <channel> ! ( <ptrn> [, <ptrn>]\* )

\| <ptrn> \| <ptrn>

\| \*<channel>

\| <name> ( <ptrn> [, <ptrn>]\* )

\| <vptrn>

\| match <ptrn> { <var> }

<vptrn> ::= <strptrn>

\| <aptrn>

\| <lptrn>

Behavioral types

<property> ::= property <name> ( <var> [, <var>]\* ) = <type>

<type> ::= true

\| Void

\| ~<type>

\| <type>&<type>

\| <type> \| <type>

\| hidden <var>[, <var>]\* . <type>

\| <channel>?<type>

\| <channel>!( <type>[, <type>]\* )

\| <name>( <channel> [,<var>]\* )

Syntactic Sugar

Replication

Notice that when the reduction rules of the semantics section are used,
the process

x!( for(y <- x){ x!(\*y) \| \*y } \| P )

\| for(y <- x){ x!(\*y) \| \*y }

reduces to

P \| P \| …

In other words, this expression constitutes an implementation of
replication, and thus the code context for P represents a concurrent
version of the famous Y-combinator for the lambda-calculus.

It is well known that recursion and replication are inter-definable, and
providing a version of recursion from replication is a useful and
instructive exercise. In this connection, notice that it is always
possible to convert an input guarded process expression into one that
will receive its continuation from an outside source. That is,

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

*data consumer* \| *data producer*

Data ephemeral, continuation ephemeral

This is the standard process calculus expression where both the
continuation and the data are ephemeral. That is, the channel is used
exactly once by the consumer, and the data is removed from the channel
once it’s read:

for(v <- channel) P \| channel!(v)

Data persistent, continuation ephemeral

This means that the channel is used as a memory location, i.e. the value
will remain in the channel after it has been read. Traditionally, this
is decided by the producer of the data, by sending it using two
exclamation marks instead of one, like so:

for(v <- channel) P \| channel!!(v)

The expression above is sugar for

for(v <- channel) P \| $(channel!(v))

However, sometimes it is useful for the consumer to only peek at the
value in a channel, even if the producer didn’t specify that it should
persist. This is achieved by using the assignment operator :=, as here:

for(v := channel) P \| channel!(v)

This is interpreted as the consumer simply removing the value and then
resending it, i.e. it is syntactic sugar for:

for(v <- channel) (channel!(v) \| P) \| channel!(v)

Of course, these two can be combined. Both the producer and the consumer
could want to make sure that the value will stay in the channel, which
they express by:

for(v := channel) P \| channel!!(v)

Desugaring this using the rules given in the previous two examples, we
see that this is sugar for:

for(v <- channel) (channel!(v) \| P) \| $(channel!(v))

Data ephemeral, continuation persistent

If the consumer wants to listen to a channel as a stream, it wants to
execute the same continuation for every message that is received. In
other words, the continuation should persist. We signify this using the
stream operator <<, as shown below:

for(v << channel) P \| channel!(v)

Again writing $P for replication, this is sugar for:

$(for(v <- channel) P) \| channel!(v)

Data persistent, continuation persistent

Finally, we come to the case where both the data and the continuation
should persist, i.e.:

for(v << channel) P \| channel!!(v)

There are two natural interpretations of this expression. One is that it
corresponds to an infinite loop and should be caught by the compiler. To
see this interpretation more clearly, we simply desugar using the rules
given above, and get:

$(for(v <- channel) P) \| $(channel!(v))

That is, the same value should be sent over the channel infinitely many
times, and it should be read and passed to the continuation infinitely
many times.

The other interpretation is that this corresponds to a data stream with
persistent history. This seems like a more useful interpretation to us.
One possible desugaring would be

$(for(v <- channel) { P \| h(channel)!(v) }) \| channel!(v))

Combining persistence choices

An interesting case arises when we attempt to join a stream with a
one-off channel, as here:

for(v << state; t <- channel; if cond(v,t)) P

 \| state!(v)

 \| channel!(t)

There are two natural interpretations that immediately spring to mind.
One is that the one-off channel effectively turns the stream into a
one-off, i.e. it would be semantically equivalent to

for(v <- state; t <- channel; if cond(v,t)) P

 \| state!(v)

 \| channel!(t)

The other interpretation is that the stream effectively turns the
one-off channel into a stream, by replicating the first value that
appears on channel. In other words, it would be interpreted as
semantically equivalent to

for(v << state; t := channel; if cond(v,t)) P

 \| state!(v)

 \| channel!(t)

It is interesting to note that under this interpretation, the latter
interpretation would very easily give rise to the following race
condition:

for(v << state; t << channel; if cond(v,t)) P

 \| state!(v)

 \| channel!(t)

 \| channel!(t’)

Here, one of t and t’ will be duplicated, because of the interpretation
of := as reproduction, as it was given above. This gives slight to the
former interpretation, which we have chosen. Of course, it is still
possible to write the race manually:

for(v << state; t := channel) P

 \| state!(v)

 \| channel!(t)

 \| channel!(t’)

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

Primitives, Builtins, and Foreign Functions

One of the most important aspects of the reflective semantic model is
that it affords a modular treatment of primitive data types such as
booleans, string, numeric, and collection types, and the host of other
data types that make a modern language usable by modern standards. This
comes about in a way that will make the Lisp programmer smile: data is
code and code is data. Reflection provides a rational framework for this
well loved and heavily used duality between code and data. In the
concurrency setting this formulation amounts to saying that all data are
processes. Numbers, strings, logical values, all of these familiar
elements of modern programs are processes. However, because we can ship
processes around as if they were data, we can effect the natural usage
patterns of passing values as if they were data.

From a historical perspective it is important to note that while the
untyped functional programming languages, such as Lisp and Scheme,
wholeheartedly embraced the data/code duality and used it to provide
highly sophisticated meta-programming capabilities, it was much longer
before this was embraced by typed languages, let alone the typed
functional languages. Java was arguably the first mainstream language to
adopt a typed reflective model, and typed reflection functional
languages are still far behind Java’s level of maturity, though Scala is
definitely gaining ground here. Rholang steps past Scala by being the
first programming language ever to provide a typed reflective model of
computation that takes concurrent execution as the primitive engine of
computation. In fact, it is the only language with all of these elements
combined into a single language design.

Because it seeks to be a general purpose programming language -- that
just happens to run on a computer whose state is stored on the
blockchain -- it needs to have a rich set of data types, builtins, as
well as a means to plug in new data types and builtins coming from
external or third party components, such as linear algebra packages
(like BLAS or LAPACK), or SAT-solvers or any of a number of other useful
computational libraries that will ease the job of writing powerful
applications and contracts. Following Milner’s lead, we note that
functions are processes! Further, because we can pass processes as data
we get higher-order function-passing capabilities for free.

Realizing the builtin data types and operations as builtin processes not
only makes the language easily extensible, it also allows for a
modularization of the semantics. Since logical, arithmetic, string, and
collection operations, along with the corresponding data elements on
which they operate, are realized as a set of builtin processes the
semantics can be factored cleanly into the core semantics that says
quite generally how processes compute and a set of builtin processes for
the primitive operations. This modularity makes it easy to maintain and
evolve Rholang. For example, a first implementation might not come with
bignums. Adding bignums can be as simple as providing the set of
primitive processes that implement the bignum representation together
with the set of transduction processes that convert between bignums and
the other numeric types.

We feel that this kind of modularity is critical in a open-source
environment, especially one such as we find with cryptocurrency and the
blockchain, where many parties will look at providing different, and
possibly slightly differentiated implementations of the specification.
Modularity makes it possible to have a unified substrate with high level
feature differentiation amongst many implementations of the language.

An example from arithmetic

Lest this seem terribly abstract, let us consider a simple example, such
as addition. Just as with modern functional languages, we recognize that
an expression like m + n is never really outside of an evaluation
context. There is always some continuation that is waiting for the
result of this expression, whether it is the REPL prompt or the storage
of the result in a variable. Once we understand that there is some
waiting continuation, say P, we can model the passage of the result to
the continuation as

 for(result <- k)P

Now, let us imagine we have a function, written [\| e \|](k), which
translates an arithmetic expression e into a process that will evaluate
e and pass the result along the channel k. Then, the expression that
evaluates e and passes it to the awaiting continuation P is just

for(result <- k)P \| [\| e \|](k)

If we adopt the convention that [\| e \|] denotes the value of e, then
the expression above becomes

for(result <- k)P \| k!([\| e \|])

Thus, if e is m+n, we have

for(result <- k)P \| k!([\| m+n \|])

Now we can recurse, compositionally evaluating m, n and +.

for(result <- k)P

\| k!(new kp in kp!([\| m \|]) \| kp!([\| n \|]) \| [\| + \|](kp))

Now, suppose

[\| + \|](kp)= for(m <- kp; n <- kp){ *m+n* }

where *m+n* denotes the primitive process representation of m+n. To be
more specific, since Alonzo Church showed how to encode the natural
numbers and arithmetic into the lambda calculus, it has become common
practice to study various encodings of arithmetic in various
computational calculi. In the polyadic π-calculus tutorial, for example,
Milner gives a natural and direct encoding of the naturals and
arithmetic into the π-calculus. Because the π-calculus can be faithfully
encoded into the rho-calculus and Rholang is merely syntactic sugar for
the rho-calculus, we can transport Milner’s encoding directly to this
setting. Alternatively, Meredith has given an encoding of Conway games
into rho-calculus, providing the basis for a Rholang bignum package that
includes the transfinites and the infinitesimals! Further still, this
particular encoding for addition can be seen as part of a recursive
Church-numeral-like encoding that bottoms out with the stopped process
representing the number 0. As interesting as these various approaches
are, the details are not salient to the example. There are lots of ways
to provide a Church-numeral-like representation of m+n. As such, we
simply assume that there is a faithful representation of the value of
m+n as a process *m+n*.

Before moving on it is interesting to note one key feature of this
encoding. The values for m and n are arriving on kp in parallel. As
such, what is bound to the *variable* m could be *m* or *n*. If it turns
out that *n* is bound to m, then *m* will be bound to the variable n.
So, the expression *m+n* will evaluate either *m+n* to or *n+m*. Because
addition is commutative these are the same. Were the operation
noncommutative, such as matrix multiplication, we could sequentialize
the communication to ensure that the arrival order was deterministic and
fixed and thus respect the intended semantics of the expression. In
fact, the interested reader is encouraged to modify the definition to
provide the appropriate sequentialization to get a feel for the
language. The main point here though, is that we don’t have to and as
such have access to a much wider range of optimization and performance
improvements in our compilation strategies.

Now, equipped with the process *m+n*, the resulting expression looks
like

for(result <- k)P

\| k!(new kp in kp!([\| m \|]) \| kp!([\| n \|]) \| for(m <- kp; n <-
kp)(\ *m+n*))

which then evaluates to

P{@(new kp in kp!([\| m \|]) \| kp!([\| n \|]) \| for(m <- kp; n <-
kp)(\ *m+n*))/result}

Any usage context in P of the form \*result will deliver the desired
result, namely *m+n*. To see this let’s consider the simplest possible
example. Suppose that P = \*result. Then we will get

\*result{ @(new kp in kp!([\| m \|]) \| kp!([\| n \|]) \| for(m <- kp; n
<- kp)(\ *m+n*))/result}

=

new kp in kp!([\| m \|]) \| kp!([\| n \|]) \| for(m <- kp; n <-
kp)(\ *m+n*)

->

new kp in *m+n*

Since kp is never mentioned in *m+n*, it may be garbage collected,
resulting in

*m+n*

There are a few points worth calling out in this implementation. First,
this implementation resembles nothing so much as a register machine
pushing m and n into a register kp, and then having the addition
operation + pop the register to perform the add op. As such, a compiler
would be well in its rights to compile to just such an implementation
scheme.

Second, most programmers don’t care about the internal representation.
They just want to make sure that when they store 5 in a variable x, i.e.
they publish the value 5 to the location associated with the channel x,
and they store 7 in a variable y, likewise publishing 7 to channel y,
when they add x and y, i.e. read from channels x and y and adding the
results, they get 12. Yet, reflection coincides with this very pragmatic
outlook. The *numeral* 5 can be viewed as the code for the process that
represents the *number* 5 represents. If m and n are respectively 5 and
7, then @(m+n) is the code for the process representing the number
indicated by 5+7. Arguably, the *numeral* 12 (or 1010 in binary or 14 in
octal or … ) is most natural code for the process representing the
*number* represented by 5+7. More generally, the most natural
implementation of an expression like @(m+n) is simply the corresponding
numeral.

Third, the implementation is lazy. It defers the actual calculation of
the addition until the point at which the value is needed. Languages
like Haskell, and to some extent Scala, have made a successful case for
lazy evaluation. It is important to ensure, however, that such lazy
evaluation schemes are not brittle, in the sense that a small change in
the representation of a data structure constitutes a non-local change to
the computational complexity of a program that has some ultimate
dependency on the data structure. This is a criticism that Haskell
suffers. Here, however, the laziness is effectively a compile-time
rather than runtime phenomenon, in the sense that a good optimizing
compiler could analyze the contents of the publication on k and reduce
it directly the value it represents, without negative impact on the
code. Thus, it is arguably less susceptible to the kind of lazy
evaluation brittleness found in Haskell.

Modularity, efficient implementation, and scalable verification

In this connection, it is important to understand that this example is
primarily to show how easy it is to model and integrate primitives,
builtins, and foreign functions. It is not necessary to spin up the
*four threads* executing in this translation just to effect an addition!
Any reasonable implementation will likely effect the whole operation by
mapping it to native execution, such as a JVM or LLVM operation, or
whatever the compilation target might be. However, a non-native
implementation and semantics can always be provided.

The ability to have process level and/or native level representations of
various data types and operations should be familiar to those people who
have worked with managed code environments like the JVM or the CLR.
Boxing and unboxing in those settings is an instance of the same
phenomenon. This feature allows for modular reasoning about the
correctness of the language implementation as a whole, and thus the
correctness of contracts with dependencies on various modules providing
primitives data types and operations.

Specifically, correctness can be reasoned about on a module by module
basis, which is critically important for a practical formal verification
programme to work. It would be impossible to release an implementation
of Rholang if all primitives and operations, from floating point
arithmetic to date-time operations, had to be formally verified correct
first. These are extremely large surface areas and notoriously difficult
to verify. However, because Rholang’s semantics is modularly organized,
an implementation that relies on a native, formally verified arithmetic
library or module, will enjoy the confidence that the questions about
the correctness of contracts using (only) that library will be limited
to the contract implementations themselves.

\ **Semantics**

A specification of the rho-calculus with summation

What follows is a minimal specification of the reflective higher-order
π-calculus (aka rho-calculus) with summation.

Syntax

M,N ::= 0          // nil or stopped process

       \|   for( x1 <- y1; … ; xN <- yN )P          // input guarded
agent

 \| x!( P ) // output

       \|   M+N       // summation or choice

P,Q  ::= M          // "normal" process

 \|   \*x          // dereferenced or unquoted name

       \|    P\|Q        // parallel composition

x,y ::= @P          // name or quoted process

Free and bound names

FN( 0 ) = {}

FN( \*x ) = { x }

FN( for( x1 <- y1; … ; xN <- yN )P )

= { x1, … , xN } U FN( P ) \\ { y1, … , yN }

FN( x!( P ) ) = { x } U FN( P )

FN( M+N ) = FN( M ) U FN( N )

FN( P\|Q ) = FN( P ) U FN( Q )

Write the reduction rules. This spec isn’t useful yet because it doesn’t
specify how to

Structural equivalence

Structural equivalence is the smallest congruence, =\ :sub:`S`, such
that

-  (P,\|,0) form a commutative monoid

-  (P,+,0) form a commutative monoid

-  If =\ :sub:`N` denotes name-equivalence, then =\ :sub:`S` includes
   the alpha-equivalence using =\ :sub:`N`

Name equivalence

Name equivalence is the smallest equivalence on names such that

P =\ :sub:`S` Q => @P =\ :sub:`N` @Q

Semantic versus syntactic substitution

See: \ `*L. Gregory
Meredith* <http://docs.google.com/m/Meredith:L=_Gregory.html>`__,
Matthias Radestock: A Reflective Higher-order Calculus. \ `*Electr.
Notes Theor. Comput. Sci.
141* <http://docs.google.com/db/journals/entcs/entcs141.html#MeredithR05>`__\ (5):
49-67 (2005)

for a detailed account. Terms of the form \*x are taken to Q when a
substitution of the form { @Q/u } is applied and x =\ :sub:`N` u.

Reduction relation

comm: xi =\ :sub:`N` xi’ => R + for( x1 <- y1; … ; xN <- yN )P + S \|
x1’!( Q1 ) \| … \| xN’!( QN ) -> P{ @Q1/y1, … , @QN/yN }

par: P -> P' => P\|Q -> P'\|Q

struct: P = P', P' -> Q', Q' = Q => P -> Q

Guidance for implementations

Ignoring the nuances around the structure of names, here is a perfectly
reasonable rendering of the core concurrency semantics into Scala code.

|image5|

.. [1]
   :sup:`` More accurately, Factom is putting hashes of land trust
   records into their blockchain and anchoring this to the bitcoin
   blockchain.

.. |image0| image:: Pictures/1000020100000126000000AC807ACB285D9615DB.png
   :width: 1.67010in
   :height: 0.97910in
.. |image1| image:: Pictures/1000020100000113000000B7566CC1749F40CC7F.png
   :width: 1.59840in
   :height: 1.06100in
.. |image2| image:: Pictures/1000020100000103000000C2C872DB296567CC5E.png
   :width: 1.86930in
   :height: 1.39570in
.. |image3| image:: Pictures/1000020100000114000000B791407AD6C826B151.png
   :width: 1.82800in
   :height: 1.20710in
.. |image4| image:: Pictures/100002010000008C0000008C8035F97AEF0AC353.png
   :width: 1.45830in
   :height: 1.45830in
.. |image5| image:: Pictures/10000000000002F400000229B81C359AFFA72AA9.jpg
   :width: 6.50000in
   :height: 4.75000in
