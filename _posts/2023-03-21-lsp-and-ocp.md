---
layout: post
title:  "lsp and ocp"
date: 2023-03-21
categories: patterns
upload-date: 2023-03-21
---

violating liskovs substitution principle always violates open closed principle.
but violating open closed principles may or may not violate liskovs substitution principle.

simple example,
violation of lsp.

when a function takes a interface or rather a type. which may have different implementations or rather subtypes.
and then that function is doing if else/switch based on type.
calling, or expecting different results based on type. then its violating lsp. and since for every type a case have to be added. this function is not closed to modification violating ocp.

now lets take another example where, 
a function takes a string, parses it and creates a correct implementation.
return type is an interface.

this method is not violating lsp.
but its is violating ocp.
cause for each new type new case has to be added here.

and tbh that is fine.
you can't make everything adhere to ocp.
but you can constrain them to a minimal part of your system.

rather than switching in everywhere.

like a list can be open to modification but apart from that everything else is closed...

thats the thing...

https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html


