---
title: "A Proposed Evolution of Postel's Principle"
abbrev: "A New Postel's Principle"
docname: draft-thomson-postel-was-wrong-latest
date: 2015
category: info
ipr: trust200902

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: M. Thomson
    name: Martin Thomson
    org: Mozilla
    email: martin.thomson@gmail.com


informative:
  RFC0760:
  RFC4566:
  RFC6709:
  RFC7159:
  RFC7230:
  RFC7423:
  I-D.ietf-json-i-json:


--- abstract

Jon Postel's famous statement in RFC 1122 of "Be liberal in what you accept, and
conservative in what you send" -- is a principle that has long guided the design
of Internet protocols and implementations of those protocols.  The posture this
statement advocates might promote interoperability in the short term, but that
short term advantage is outweighed by negative consequences that affect the long
term maintenance of a protocol and its ecosystem.


--- middle

# Introduction

Of the great many contributions Jon Postel made to the Internet, his remarkable
technical achievements are often ignored in favor of the design and
implementation philosophy that he first captured in the original IPv4
specification [RFC0760]:

> In general, an implementation should be conservative in its sending behavior,
  and liberal in its receiving behavior.

In comparison, his contributions to the underpinnings of the Internet, which are
in many respects more significant, enjoy less conscious recognition.  Postel's
principle has been hugely influential in shaping the Internet and the systems
that use Internet protocols.  Many consider this principle to be instrumental in
the success of the Internet as well as the design of interoperable protocols in
general.

Over time, considerable changes have occurred in both the scale of the Internet
and the level of skill and experience available to protocol and software
designers.  Part of that experience is with protocols that were designed,
informed by Postel's principle, in the early phases of the Internet.

That experience shows that there are negative long-term consequences to
interoperability if an implementation applies Postel's principle.  Correcting
the problems caused by divergent behavior in implementations is extremely
difficult or impossible.

It might be suggested that the posture Postel's principle advocates was indeed
necessary during the formative years of the Internet, and even key to its
success.  This document takes no position on that claim.

This document instead describes the negative consequences of the application of
Postel's principle to the modern Internet.  It also proposes a replacement
design principle.

There is good evidence to suggest that designers of protocols in the IETF widely
understand the limitations of Postel's principle.  This document merely serves
to document the shortcomings of His principle to the wider community.


# The Long Term Consequences of Liberal Acceptance {#time}

Divergent implementations of a specification often arise when the specification
fails to cover a particular case properly.  This can be a lack of specification,
or a conflict in specification requirements.

Aside from specification shortcomings, similar problems emerge when the protocol
enjoys use outside of its original intended context, or when the protocol is
extended to support new scenarios.

Finally, if implementation flaws in protocols are not detected and fixed
quickly, they can remain as a source of implementation variation.

Situations where two peers disagree about the exact interpretation of a
specification are common, and should be expected over the lifetime of a
protocol.

An implementation that reacts to variations in the manner advised by Postel's
principle is expected to be tolerant.  This sets up a feedback cycle of negative
consequences:

* Over time, implementations progressively add new code to constrain how data is
  transmitted, or to permit variations what is received.

* Errors in implementations can thereby be masked.  Subsequently, these errors
  can become entrenched, forcing other implementations to be tolerant of those
  errors.

For instance, a number of bugs (or quirks) in both specifications and
implementations of the JSON format [RFC7159] mean that certain encodings can
cause problems.  A safe subset of JSON has been defined that identifies
constructs that can be avoided to maximize interoperability
[I-D.ietf-json-i-json].


# Creating Barriers to Implementation

Once divergent implementations exists, interoperation with those implementations
generally takes precedence over correcting the underlying flaws that produced
the divergence.  This is both a natural consequence of applying Postel's
principle, and a natural reluctance not to generate an error condition when
the situation can be recovered.

Once this process has begun and deviations become entrenched, there is little
that can be done to reverse the process.

Protocol maintenace can help by carefully documenting the various forms of
divergence and recommending limits on what is acceptable.  This process can be
tedious and time-consuming.  For instance, the revision to HTTP/1.1 took
considerable effort over more than 6 years.  However, this sort of effort was
crucial in supporting the interoperability of both existing and new protocol
implementations.

For widely used protocols, the current scale of the Internet makes large scale
interoperability testing infeasible for all a privileged few.  Without good
maintenance, new implementations can be restricted to niche uses, where
interoperability concerns cause fewer problems.

This has a negative impact on the ecosystem of a protocol.  New implementations
of a protocol are important in ensuring the continued viability of a protocol.
New protocol implementations are also more likely to be developed for new and
diverse use cases and often are the origin of features and capabilities that can
be of benefit to existing users.


# Extension: Elephants Out, Donkeys In

Many protocols are specifically designed to permit deviation from the basic
protocol in constrained ways.  A well-defined extensibility framework in a
protocol is often the most valuable asset a protocol has.  For instance,
Diameter's comprehensive extensibility framework [RFC7423] is perhaps its most
important feature; whereas the Session Description Protocol [RFC4566] has
benefited greatly from the ability to extend with new attributes.

It is recognized that designing and defining an extension framework is
challenging [RFC6709].  In particular, it is difficult to anticipate the various
ways in which future protocol users might wish to expand the protocol.

Extension of a protocol without regard to the existing extension points -
especially in an ad-hoc fashion - is a common source of implementation
divergence.


# A Fatalistic Interpretation of the Principle

As shown in {{time}}, an implementation that adheres closely to Postel's
principle will, over time, be increasingly constrained in its behavior.  The
messages it transmits will be more and more tighly proscribed and it will have
increasingly promiscuous criteria for acceptance of incoming messages.

Thus, a more appropriate interpretation of Postel's principle might be as an
observation that the Second Law of Thermodynamics applies to an unmaintained
protocol.  That is:

> Over time, the pressure to favor interoperability over correctness leads to
  constraints on transmission and a need to be tolerant in reception.

After all, even with the best intentions, the pressure to interoperate can be
significant.  No implementation can hope to avoid having to make a decision
regarding the trade-off between correctness and interoperability forever.


# A New Design Principle for the Internet

The following principle applies not just to the implementation of a protocol,
but to the design and specification of the protocol.

> Protocol designs and implementations should be maximally strict.

Though perhaps a less pithy than Postel's formulation, this principle is based
on the lessons of protocol deployment.  The principle is also based on valuing
early feedback, a practice central to modern engineering discipline.

## Fail Fast and Hard

Protocol designers should attempt to identify all sources of potential errors
during the specification process.  More importantly however, protocols need to
include error reporting mechanisms that ensure errors are surfaced in a visible
and expedient fashion.

Generating fatal errors for what would otherwise be a minor or recoverable error
is preferred, especially if there is any risk that the error represents an
implementation flaw.  A fatal error provides excellent motivation for addressing
problems.

On the whole, implementations already have ample motivation to prefer
interoperability over correctness.  The primary function of a specification is
to proscribe behavior in the interest of interoperability.


## Extend Carefully

Special attention needs to be paid to the design of protocol extensibility.  An
extension framework [RFC6709] can be the source of significant variation in
implementations if it is poorly designed, but a well designed extension
framework can relieve pressure on protocol maintainers.

A protocol extension point carries both cost and risk.  It is better to remove
extension options than to risk providing an extension mechanism with unknown or
uncertain characteristics.

Version negotiation is perhaps the most critical extensibility point for any
protocol, and even protocol extensions.  Version negotiation provides an option
of last resort for any shortfalls in any originally conceived extensibility:
changes that can't be accommodated within the protocol can be addressed by
revising the entire protocol.  A protocol that includes robust provisions for
its own eventual replacement always has at least one option available to address
even the most intractable of maintenance problems.


## Implementer Guidance

Implementers are encouraged to expose errors immediately and prominently in
addition to what a specification mandates.

Exposing errors is particularly important for early implementations of a
protocol.  If existing deployed implementations generate errors in response to
divergent behaviour, then new implementations will be able to detect and correct
flaws quickly.

An implementer that discovers a scenario that is not covered by the
specification does the community a greater service by generating a fatal error.
Hiding error scenarios does significant long-term damage to a protocol.
Ideally, specification shortcomings are taken to protocol maintainers.

This guidance does not demand slavish and unreasoning adherence.  Protocol
designers are capable of determining that a particular problem requires
tolerance.  However, capturing that decision in a specification is critical to
avoid later issues.


## Protocol Maintenance is Important

Protocol designers are encouraged to continue to maintain and evolve protocols
beyond their initial inception and definition.  If protocol implementations are
simultaneously less tolerant of variation and less extensible, protocol
maintenance becomes critical.

Being responsive to community needs demands an ongoing commitment to improving a
protocol.  That means both addressing specification bugs and a willingness to
consider the addition of features.  Outright rejection of a feature can force
requesters to explore ad-hoc protocol changes.  A poorly designed ad-hoc change
can do significant harm to interoperability if they turn out to be successful.


# IANA Considerations

This document has no IANA actions.


# Security Considerations

Sloppy implementations, lax interpretations of specifications, and uncoordinated
extrapolation of requirements to cover gaps in specification can result in
security problems.  Hiding the consequences of protocol variations encourages
the hiding of issues, which can conceal bugs and make them difficult to
discover.

Designers and implementers of security protocols generally understand these
concerns.  General-purpose protocols are not exempt from careful consideration
of security issues.  In fact, because general-purpose protocols have to deal
with flaws or obsolescence in a less urgent fashion than security protocols,
there can be fewer opportunities to correct problems in protocols that exhibit
problems.
