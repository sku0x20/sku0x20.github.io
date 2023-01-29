---
layout: post
title:  "test method name"
date: 2022-06-06
categories: test
upload-date: 2023-01-29
---

should test names be starting with 'should'???
    goos nope, (rare case)
    james nope, (rare case)
    idk or remember from where i learnt this thing that naming of every test should start with should??
        maybe when reading online i found that tests name should start with should, and i am just following that practice.
        idk, how good it is, cause many times it makes the name longer and sometimes even convoluted,
        making harder to know what we are testing...
    needs to re look this topic...
    https://stackoverflow.com/a/7398606
    "should" make it a bit repetitive, but it helps me in thinking what i want, the behaviour.
    and as they say, it's good for novice programmers like me... :)
    but i do want to say to myself, it need not start with should.
    it's more than ok if you include it in between or does not include it.
    just, make sure you understand, what you are testing...
    should enforce the behaviour we want. without should in the method name, it's like ok, this class does this.
    this is the behaviour of this class. with should it like, this class should do this. it's like more enforcement.
    see this, shouldNotGenerateSequence vs numbersShouldNotBeInSequence vs shouldGenerateNonSequentialNumbers vs generatesNonSequencialNumbers
    without should it's like, what does this class do, yeah it generatesNonSequentialNumbers, any other thing, yeah it does that/this...
    with should it's like, what does this class do, ok it should generateNonSequentialNumbers, any other thing, ok it should...
    and with another flavour of should, numbersShouldBeInSequence/generatedNumbersShouldBeInSequence. with this approach we can't even ask ourselves, what does this class do. it "numbers.."? does not make sense.
    what i am missing is the documentation part,
	what does this method test, it test that "numbersShouldBeInSequence" is best self-documentation, but it lacks the documentation for the class under test.
	where as with should... it's like, what does this method test/do, it tests that this class shouldGenerateNonSequentialNumbers. which is better.
	still, you're seeing with should it's like, it should but does it in reality?
	ok, last one without should, what does this method test/do, it tests that this class generatesNonSequentialNumbers, which much better.
	it's not it should rather it does...

ok so my work flow will be a bit changed from now on,
first like everytime, just ask myself what behaviour i want from this class. how i want it to behave.
i ask, if it should behave this way. like should it generateNonSequentialNumbers??? then i answer that question.
if it should, i write test method as it "generatesNonSequentialNumbers". it's like i am saying, what does this class do.
it generatesNonSequentialNumbers.
reading the methods is a bit different, one should read them as what does this method test/do. and the answer is something like,
this method/it tests that this class generatesNonSequentialNumbers

also, as i always say every situation is unique, so like when you are testing the setters and getters, you can just say setsAndGet or something like that.
also, like is case of value object/type one can name test method as valueObject and when reading he/she can just ask this method tests that this class is/should be a value object.
so it kinda depends.
don't constrain yourself too much but also don't give yourself free hand too much, follow conventions but don't get bounded by them...
and as always it' GOOD ENOUGH(for now)...

