---
layout: post
title:  "A response to James Shore's Nullable pattern
category: programming
tags: 
  Testing
  Refactoring
  Test Driven Development
---

I've been reading James Shore's pattern language, 
[Testing without Mocks](https://www.jamesshore.com/v2/projects/testing-without-mocks/testing-without-mocks) with interest.
It has some interesting ideas and, for me, some points of disagreement. 

My first objection is to the "clickbaity" title, as if mocks were 
some catastrophic evil, rather than one tool amongst many. An interesting
pattern language has to be more than an opposition to a minor technique.

I also see some confusion between concept and tooling. James has since written 
another post that clarifies his wariness about 
[Dependency Injection frameworks](https://www.jamesshore.com/v2/blog/2023/the-problem-with-dependency-injection-frameworks)
but has not yet made the same point about mocking frameworks.
And many of the popular frameworks are not ones I would chose. Unfortunately, almost our 
entire modern stack is based on widely-adopted but flawed implementations--
the Unix API being a find example. 

Once again, I have to take on the argument that testing interactions 
means testing implementation. There's a fundamental disagreement here. 
To me, for some objects the calls they make to their surrounding environment, 
in response to calls they receive, *is* the interesting behaviour I want to test, not a detail of implementation. 

This is not the only way to do design, but there is an established 
approach to object design that focusses on the messages they send to each other, 
dating back to Smalltalk and Erlang. Ralph Johnson called this the "mystical" school
of objects. There are other schools of object design where this is less interesting.

It is perfectly possible to write tests that express these interactions clearly, 
just as it is possible to make a complete mess of them. Most of the bad testing
I see comes from not understanding the concept and, in particular, 
testing at too fine a grain. 

The most confusing part to me is that the proposed techniques, 
combining Nullable objects with Configurable Responses and Output Tracking
sounds exactly like what we do with mocks, except perhaps with the substitutions
plugged into different places.

## State-Based Tests

The state vs interaction example that James uses is not one I would recommend. First, 
the calls that it overwrites are static, rather than methods on an object that is 
passed in, so it's not forcing the dependency. Second, we have a heuristic of 
"Stub queries, expect actions", so I would not set expectations on simple queries;
by about the third test, I would have factored these out into common setup. Third, 
Moon looks like the sort of object that I would be using in the test directly, 
not stubbing out. Given the amount of Reflecto-magic involved, I'm not surprised
that these tests run so slowly.

And, by the way, did anyone notice the Feature Envy?

James' point about localising tests is important. I see one way of coping with scale
it that each level of code tests its own paths, assuming that detail will be 
tested further down the line.

## Zero-Impact Instantiation

I absolutely agree that constructors should be simple. My approach is that the
only thing a constructor should do is to _construct_ the object, that is assign
its fields. If I have more work to do to get there, then I write a factory
method or function. I find that this ensures that objects are easy to create
in situations I hadn't planned for, like new tests. This is a technique I learned
years ago from Modula-3 (which I still miss).

If I can, I avoid `connect()` or `start()` methods on an object, 
preferring to do that work outside the object and only constructing 
it if I already have a working connection. Of course, that depends on 
the runtime lifecycle.

## Parameterless Instantiation
For code of any size, I often end up building common infrastructure for creating
test objects, using helper methods with descriptive names so that the tests code
describes just what is immediately relevant. Sometimes I can default out all the 
parameters, sometimes I need to make a choice first. That pushback from the 
constructor interface is valuable, it shows me in the code how objects are connected.

## Signature Shielding
Very much agree with this. Test code needs to be treated with the same care and attention
as production code, possible even more because it bridges the domain and testing. I see
many codebases where the reason the tests are brittle is because they've never been 
refactored. One caveat is that sometimes I allow a bit more duplication in tests to 
keep them more readable.

## A-Frame Architecture, Logic Sandwich, Traffic Cop
All good points. Quite often I end up with a higher-level directory that contains 
the core concepts of the code, as interfaces and values, with lower-level directories
for various aspects of the implementation.

## Grow Evolutionary Seeds
I think we're less far apart than James presents. The flow he describes is similar to
how an outside-in approach would start, especially the hard-coding of values. One 
advantage for me of outside-in is that it helps me to think about and discuss what 
we want to achieve next. Much of the rest of this process is familiar.

## Early-Visible Behaviour

Agree on most of this, except for providing a getter just for testing, to me that's 
exposing implementation because it locks in some aspect of the object's internal 
representation. The alternative, exposing an event to be listened for, sounds like 
a standard Mock expectation.

## Testable libraries

Largely agree here. This is why Ports-and-Adapters is not just for services as a whole
but is a useful mid-level pattern within a codebase.

### Collaborator-Based Isolation

This is an interesting idea. I tend to build up a collection of representative constants
that I use as markers in tests and their setup, so it would be `INVENTORY_ADDRESS` rather than
`123 Main Street`. In our write-up of the builder pattern, we provide defaults for
common values so that the didn't need to be specified repeatedly.

### Infrastructure Wrappers

Again agree. What's not made explicit is that I use Interface Discovery to pull in just the features
my code needs from its environment, rather than providing a generic clean veneer over the external 
service. Quite often, after a bit of refactoring, I end up with an external service translator that
implements a small set of interfaces driven by the needs of various bits of the main code.

### Narrow Integration Tests, Paranoic Telemetry
Yes and yes

## Nullables
This is an interesting idea. My first question is whether, if one has a version of an object which
behaves partially differently depending on circumstances, is whether this is actually more than one object
and that the nullable part should be factored out.

## Embedded Stub
While I absolutely agree with keeping things minimal, I struggle with this one. 

First, if I need to put it close to the production code to remind me to keep up to date,
then surely I'm missing some tests somewhere? Second, a constant complaint about mocking third-party code is the
difficulty of ensuring that it's compliant, which is why we recommend against doing that. How does one keep
third-party code stubs compliant in this approach?

## Configurable Responses
Yes. This is exactly how we started with our fist mock testing, then we got bored of the duplication
and wrote some supporting libraries. We kept it as simple as we could. I struggle to understand why 
basic libraries are overkill for stubbing but not for, say, string operations or, even, unit testing.

## Output Tracking
As mentioned above, this sounds remarkably like mock expectations, with the disadvantage that one
can't trip the debugger with all the state in place when something goes wrong. 

I wrote a chapter about using the need for event listening to guide collaborator discovery in 
the GOOS book. There was a time when introducing a listener for testing, as against using 
the log, was a radical concept.

## Fake It Once You Make It
Again good stuff here. I have a question around the embedded use of Nullables. I like to use the back
pressure of seeing too many parameters being passed in as a hint for missing constructs. If those 
instances are created as default nullables, rather than being declared as an explicit dependency, then 
I can't see that.

## Replace Mocks with Nullables
I'm seeing most of this as a shift in terminology, rather than a meaningful redesign. Maybe that's 
useful enough. One thing I can guarantee is that if this pattern really takes off, then it will be 
abused, and then trashed for that abuse, in ways that James never thought possible.


