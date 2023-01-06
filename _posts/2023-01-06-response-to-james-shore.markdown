---
layout: post
published: false
title:  "A response to James Shore
category: programming
tags: 
  Testing
  Refactoring
  Test Driven Development
---

I've been reading James Shore's pattern language, [Testing without Mocks](https://www.jamesshore.com/v2/projects/testing-without-mocks/testing-without-mocks) with interest.
It has some interesting ideas and, for me, some points of disagreement. 

My first objection is to the "clickbaity" title, as if mocks were 
some catastrophic evil, rather than one tool amongst many. An interesting
pattern language has to be more than an opposition to a minor technique.

I also see some confusion between concept and tooling. Both Dependency Injection
and Mock Objects are concepts that are often implementated by a framework and, yes,
most of those frameworks are not ones I would chose. Unfortunately, almost our 
entire modern stack is based on widely-adopted but flawed implementations--
the Unix API being a find example. 

Once again, I have to take on the argument that testing interactions 
means testing implementation. There's a fundamental disagreement here. 
To me, for some objects the calls they make to their surrounding environment, in response to calls they receive, *is* the interesting behaviour I want to test, not a detail of implementation. 

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

