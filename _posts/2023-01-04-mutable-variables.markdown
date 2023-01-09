---
layout: post
title:  "An unreasonable dislike of mutable variables"
category: programming
tags: 
  Testing
  Refactoring
  Immutability
---

I've had an adverse reaction to mutable variables for some time. 
Recently I found another justification for this paranoia.

I was working on a codebase that included a snippet that looked like this:

```python
all_the_stuff = []
if one_thing:
    all_the_stuff = search_for_one_thing()
elif another_thing:
    all_the_stuff = search_for_another_thing()

do_something_with(all_the_stuff)
```
The build also included a test coverage check, and this section showed as fully covered.

While experimenting with some refactoring, I replaced the variable with a function. 
My motivation for this is to simplify the "narrative" at the top level of the code,
pushing the detail into a supporting level, and I use mutable variables as a hint 
for where I might do that.

```python
do_something_with(all_the_stuff())

def all_the_stuff():
    if one_thing:
        return search_for_one_thing()
    if another_thing:
        return search_for_another_thing()
    return []
```

This passed the tests but failed the build because I had regressed the level of test coverage. 
It turned out that the line: 
```python
return []
``` 
was not covered. 

This showed that there was no test for the case when 
neither `one_thing` nor `another_thing` was true. This was impossible to see 
in the coverage trace for the previous version.

This gap is a strong hint to take a closer look at the code
to check whether that path is possible and to either write a test, 
if it is, or rework the code to make it clear that it's not.
