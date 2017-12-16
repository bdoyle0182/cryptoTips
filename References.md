---
layout: page
title: Randomness
description: How to avoid security holes with bad use of SecureRandom
---

**When generatingaaa Randomness**

If your randomness is being used in any context related to information you want to be kept private or secure, **never** use a regular random number generator, and **always** use **SecureRandom** to guarentee security. 

The main thing to remember when using SecureRandom is to **never** use static seeds. SecureRandom randomly seeds itself when initialized, so, unless you really need to have a seed, it is safe to just initialize it without one. 

A **BAD** Example: 
```java
String text = "not_secure";

byte[] mySeed = text.getBytes(); 

SecureRandom ranGen = SecureRandom.getInstance("SHA1PRNG"); 
ranGen.setSeed(mySeed); 
```

A **GOOD** Example: 

```java
SecureRandom ranGen = new SecureRandom(); 

```
