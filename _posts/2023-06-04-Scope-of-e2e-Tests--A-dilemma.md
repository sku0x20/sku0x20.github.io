---
layout: post
title:  "Scope of e2e Tests — A dilemma"
date: 2023-06-04
categories: test
upload-date: 2023-06-04
---

(also available at <https://medium.com/@duckydude20/scope-of-e2e-tests-a-dilemma-2b6246338add>)

### Should be Broad

testing the final image. a full blackbox testing.
eg. for jar as the final product, one should be starting that and testing.
if its a container image, should be testing that.
e2e tests should be customizeable/abstract and on occasions tests both image and jar.

how its deployed and all is not its concern.
if the kubernetes config file is correct or not.
these tests might use those config files customized versions to tests. but they are testing the final product, an image or a jar or whatever.

again as i said that source code of the test need not do all this rather the use of scripts, gradle task or like makefile in go and whatnot to setup the server, make it ready and then asserting again that url.

again there are considerations.
you can’t have e2e tests like this on android, quite hard.
point is the scripts should be swapable.
the code should be able to test anything no matter how the server is running.

### Should be Contained

apart from that being hard.
my counter intuitive point is how many times its going to change how that jar is being formed, dockerfile, etc.

source will be changed much often.
and in any case you can just gave a simple version check to see if the image is correctly formed. a simple health check.
or like simple checks which checks validly of the image and not the code. that the libraries in the image are linked properly.
if java is not interpreting properly in image that’s issue with java. integration issue right there.
it’s a java issue, i am not saying to neglect but it’s not the application code issue.
java projects testing has issues if it was unable to detect these issue.
as the saying goes one shouldn’t be testing library’s code, same thing applies. rather test if the integration code is correct.

so these jar tests, image tests are actually integration tests. checking if that image is correct, the gradle plugin configured correctly.
which can be just an application version check or sometimes a bit more.
do tdd here. do not write that docker file or that plugin config.
find out how will i know this works correctly, then write a script or whatever and assert. see the failure and then only complete the loop by writing that plugin config.
do tdd. red green refactor loop.

in accordance with this, load test should be done against a production server, maybe a clone/replica. it includes everything from development to deployment. i like to think, jar or image comes under deployment than development.
and there are different tests for deployment, but they are tbh out of scope of e2es.
e2e is how your code is integrating with each other, from the main execution point. how its reached there is a different thing. it can reach via 10s of different methods. and each have a variety of way to test. what we are testing here is integrity of the code i/you/we wrote. not how its going to be packed and deployed.

take analogy of a syrup factory. i am testing the liquid quality. rather than those filled bottles. like taking the liquid as it’s being formed before poured into the bottles.
the packing can have issues, like bottle material, eg. plastic reacting leading to color change, taste change, not properly sealed, cap has flaws, leaking, all that. but e2e is not concerned about that. that’s integration issue.
and that part should be solved/tested separately.
one can run the whole e2e again and say it can check it’s validly too, but that might not be needed, and should be availed imo.
when you can just take little sample from the bottle and test small things which you might think can happen.
in this case, simple health check and/or like it’s its outputting linked libraries versions correctly…
if its passing these small integration test that means it’s running correctly.
we have already tested our code is generating correct logic. and java is responsible for making it run. abstraction. and version check verifies that image is correctly produced.
health check to see if all required libraries are found in the image and linked properly.

again nothing is fully bug free and all. bug can leak out. not from your code but from layer down below.
and that’s why we have health checks in production.
you don’t know what could go wrong when.
testing is production is different from e2e tests. it tests deployments and imo is a must, solving the bug which may rise upfront.
again this falls perfectly with why in integration tests, we only test the integrating code not the underlying library. cause we believe that library is already tested.
same applies here. just test the integration.

## Conclusion

- e2e tests the code you write.
- code can packed in different ways. thats not e2es concern.
- e2e tests the application code written is integrating properly with **“itself”**.
- each packaging can have its own integration tests depending its own context. how its integrating and all that, but in any case e2e is untouched.
- package testing should be different and be thought of as integration testing. **“test the integration part not the library.”**
  - health checks version can be used as simple and basic tests.
- in very rare cases e2e tests suits can be used but that should be avoided. **e2es should be untouched and never get infected with how the application is being packaged.**
- packaging comes under deployment. how you want it to be deployed.
  - if i take the source and call it’s main without modification to source. e2es should hold for that.
  - if packaging is changed e2es should not fail.
- **single responsibility principle.**
  - e2e should change for only one reason, ie. when the code changes. not when docker upgraded their docker file specs.that’s a different test.
