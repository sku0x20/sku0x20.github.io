---
layout: post
title:  "rust or something"
date: 2023-03-21
categories: language, rust
upload-date: 2023-03-21
---

so in rust dyn trait is a type in itself and tbh it does make sense. 
cause at runtime it is a type which stores functions location. 
implementing a trait means memory layout where to finding those impl. fns are defined. memory location is defined. for fn lookup. those variables will be populated by impl. fns location. 

also its cause rust monomorphization fns with non dyn trait parameters.

of its a direct type no need to perform lookup. just call that fn. 
if it's dyn then perform the lookup. 

i am not even involving generics here, generics gets things more dirty.
the way rust just clubbed and squashed these two concepts together. 
very complicated. 
a feat in itself but quite complicated/complex stuff... 

really liking how rust is clubbing everything together.
this borrowing, lifetimes, scopes.
heap, box, dyn (dynamic dispatch),
generics which is very complicated in itself.
type erasure. in and out in generics.
covariance, contravariant and all that...

such a complicated mess.
but really solving things...


https://en.wikipedia.org/wiki/Virtual_method_table

https://stackoverflow.com/questions/57754901/what-is-a-fat-pointer

https://stackoverflow.com/a/57754902

