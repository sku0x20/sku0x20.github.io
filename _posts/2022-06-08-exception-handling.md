---
layout: post
title:  "exception handling"
date: 2022-06-08
# add categories
upload-date: 2023-01-29
---

you know the answer to my question is simple.

first let's say how you want to log the stuff up.
second, let's do some defensive programming,
my domain will catch any exceptions from when it calls the outer code, or my wrapper catches everything,
if it's something not good, whatever the case is, we will return a NULL object(null object pattern) or, if it http a good response which maps,
the best for the issue we have, or it depends on the case.
then our domain will be safe from any uncalled exceptions, which can break the state.
also, we can let the wrapper take in the logger and can log the exception too. makes sense.
now that we need propagate the exception till ui, our domain is safe.

OR IS IT???

what if the domain throws some exceptions, where should it be handles.
the thing is we need to see how exceptions alter the state, they should be handled where they can or where they should.
And you will know more on coding only, and those cases coming, knowing more and gaining experience. Don't do Analysis paralysis...

And always remember to never replace exeptions for Result Type. As told by great guys, "Exceptions should be exceptional"...

you need to do defensive programming but at the same time, offensive programming too. i.e. fail fast...
a mix of both the worlds... things you want/know how to handle and things you don't...
things that should crash the system, things that shouldn't...

https://wiki.c2.com/?FailFast
https://wiki.c2.com/?OffensiveProgramming
https://wiki.c2.com/?DefensiveProgramming

https://twitter.com/unclebobmartin/status/1534221053580435456



bob martin says, level as in stack, but if that's the case isn't it same as result type, why use exceptions at all???
it's like you throw and the calle catches it. why it that even needed just return a result type with error code.
the issue with this appraoch is, exceptions can propagate but now you have to bubble it up by yourself...
idk, for me, it's too early to decide anything and getting biased towards one is the worst i can do...
will have to gain experience and see what situations require which one...


same answer as i was telling you, https://softwareengineering.stackexchange.com/questions/439097/
but still same thing what makes them better than ResultType?
from my understanding, exceptions should be exceptional and you can bubble up multi-levels, which is like going against bob but i think sometimes, we need to throw upto multiple levels.
it's too early to say anything, but throwing exceptions only one level up and then rethrowing is like just using ResultType, what differece does it make?? Aren't we using exceptions same as Result and maybe Result would be better suited...
idk...


C:\Users\siddh\Pictures\screenshots
now this test how will you use Result. Runnable have to specify return type as Result. with Exceptions you doesn't.
this way runnable becomes agnostic. But idk...


