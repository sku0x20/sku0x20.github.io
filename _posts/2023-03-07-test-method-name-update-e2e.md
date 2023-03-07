---
layout: post
title:  "test method name - update - e2e"
date: 2023-03-07
categories: test
upload-date: 2023-03-07
---

e2e should not be like
takeACsvAndOutputsASolvedCsv
its correct.
its specifies the behavior. 
but e2e tests names should be.
use cases.

like use it could just be solvesCsv.
a simple use case. 

unit tests contains that level of detail what is takes and actual all that.

here e2e method name should be use case whose behavior its testing.
simple, clear and concise.

so one use case of the system is that it solvesSuduko.

another is it generates sudoku.

name is implying what it does.
how it does in inside the method.

method name should imply what it does everywhere. 
how it does that is again implementation detail and its inside the function body.

and unit tests too, function name should be what it does. like what behavior it tests.
rather than how it tests. 
taking csv and all that is impl detail. 
inside fn body. 

but lets say in e2e it can take csv as well as json. then what, are both user cases. should i be testing both. 
imo, use case is only one. solveSudoku only argument is different. 

unit tests for the parser should take care of this. parsing json correctly and csv correctly. 

there test name can be, 
parsesJson.
parsesCsv

