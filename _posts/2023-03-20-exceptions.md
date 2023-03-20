---
layout: post
title:  "exceptions should be panics"
date: 2023-03-20
categories: exception
upload-date: 2023-03-20
---

why do you want to catch exceptions in between. 
if that means your state is wrong.
you are doing things imperative.
it's high coupling. your internal state depends too much on external factors. 
its should be low coupling. 
its message passing. and letting other guy do the job. 
low coupling high cohesion...

transactions is one case. apart from that, no where. 

if it can fail or rather error is a valid result.
use result. than exception.
exceptions shouldn't be used as a way for propagation.

exceptions should be exceptional and it means a terrible thing happened. and they should be caught at the system boundaries. not in between. 

not intervene the exceptions states.

if someone in between returned exception you can't complete your work. just let it pass.

catching exception reverting back and then rethrowing.
that's what i hate. 

rethrowing shouldn't be allowed tbh. 

rust got it right. and i really liked that.
there is catch but its at system boundry.
and painic can directly abort too.
that the thing.
thats what it should be tbh.
thats what exceptions are...