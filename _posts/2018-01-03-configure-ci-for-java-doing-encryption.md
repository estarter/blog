---
layout: post
title: "How to configure CI server for java projects doing encryption"
date: 2017-10-17 11:17:07
categories: java ci
---

# How to configure CI server for java projects doing encryption

## Problem
You have an CI server building the java projects in encryption domain.
These projects use a java security libs and requires a lot of random data.
The server itself may run out of entropy for `/dev/random` which caused of a nasty problems that processes are stuck on CI whereas they run smoothly on your dev machine.

## Solution part 1 - securerandom.source

First thing you want to do is to switch from `/dev/random` to `/dev/urandom`.
You can modify `$JAVA_HOME/jre/lib/security/java.security` file to set the option `securerandom.source=file:/dev/urandom`.

If for some reason you can't modify this file, there's another option for you - use special env variable that's picked up by java:
```bash
export _JAVA_OPTIONS="-Djava.security.egd=file:///dev/urandom"
```

## Solution part 2 - securerandom.strongAlgorithms

Even though random source is set to pseudo random generator, the process may still hang.
The reason can be that `SecureRandom.getInstanceStrong()` is called, so the strongest crypto source is choosen regardless to `securerandom.source`.
To change it modify `$JAVA_HOME/jre/lib/security/java.security` as following:

```
securerandom.strongAlgorithms=NativePRNGNonBlocking
```

For Linux and MacOS, the available algorithms are([full list](https://docs.oracle.com/javase/8/docs/technotes/guides/security/SunProviders.html#SecureRandomImp)):

| Algorithm     | nextBytes    | generateSeed|
| ------------- |------------- | ------------|
|SHA1PRNG       | ???          | ????        |
|NativePRNG     | /dev/urandom | /dev/random | 
|NativePRNGBlocking | /dev/random | /dev/random|
|NativePRNGNonBlocking|/dev/urandom | /dev/urandom

## It still doesn't work - how to debug?

If you want to see what your SecureRandom implementation is doing, then you can turn on debugging:
```
-Djava.security.debug="provider,engine=SecureRandom"
```

## Future reading
* [The Right Way to Use SecureRandom](https://tersesystems.com/blog/2015/12/17/the-right-way-to-use-securerandom/)
* [Myths about /dev/urandom](https://www.2uo.de/myths-about-urandom/)
