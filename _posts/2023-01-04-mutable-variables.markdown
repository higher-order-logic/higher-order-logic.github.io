---
layout: post
title:  "An unreasonable dislike of mutable variables"
category: programming
tags: 
  testing
  refactoring
  immutability
---

I've had an adverse reaction to mutable variables for some time. 
Recently I found another justification for this paranoia.

I was working on a codebase that checked test coverage as part of the build. There was a snippet
that looked like this:

```python
all_the_stuff = []
if one_thing:
    all_the_stuff = search_for_one_thing()
elif another_thing:
    all_the_stuff = search_for_another_thing()

do_something_with(all_the_stuff)
```
This showed 100% coverage and passed the build.

While experimenting with some refactoring to reduce mutability, I replaced the variable with a function.

```python
def all_the_stuff():
    if one_thing:
        return search_for_one_thing()
    if another_thing:
        return search_for_another_thing()
    return []

do_something_with(all_the_stuff())
```

The build failed because I had regressed the coverage. 

It turned out that the line 
```python
return []
``` 

was not covered. In other words, there was no test for when 
neither `one_thing` nor `another_thing` was true, 
and the coverage trace couldn't show that for the previous version.

That's a strong hint to take a closer look at the code
to see if that path is possible and to either write a test if it is, or rework the code
to make it clear that it's not.