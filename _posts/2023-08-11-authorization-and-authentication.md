---
layout: post
title:  "authorization and authentication"
date: 2023-08-11
categories: design
upload-date: 2023-08-11
---

authorization and authentication is everywhere.
it's a cross cutting concern.
you can't say its just at one place. and every components should validate for what actions it's going to do.

for example changing a device name, should check if this guy has access to this device or not. i.e. either it's the owner or it access has been shared.
but the api/spring won't check that.
it will just check if the user is allowed to call this api.
that's all.

for better, if the calling procedure is critical. and it could affect stuff.
doing authentication/access control two times is good.
spring and then inner.

doing in spring means not doing in controllers. thats all.
your domain can and i would say should do again. if it's critical part. it might do finer checks.
more domain specific checks.
spring security check is just for the api...

so even if i remove spring. i am not giving unauthorized access to things.


as these guys are saying,
do the bare minimum i
and push down it further down or center, forming an essential part of domain.
https://softwareengineering.stackexchange.com/questions/262424/where-does-authorization-fit-in-a-layered-architecture

so spring doing bare minimum to save the apis from unauthorized access.
even if i remove that, i am accessing through grpc. it won't affect my domain as domain have their own checks.

do the bare minimum.
do the essential.
single responsibility principle.
what spring is doing in my application. and to never let it do more than it's supposed to...

