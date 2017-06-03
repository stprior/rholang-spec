
****************************
Introduction and Motivation
****************************

The need for social contracts
=============================

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

.. [1]
   More accurately, Factom is putting hashes of land trust records into
   their blockchain and anchoring this to the bitcoin blockchain.

