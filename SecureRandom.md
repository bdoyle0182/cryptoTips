---
layout: page
title: SecureRandom
description: How to avoid security holes with bad use of SecureRandom
---

**When using SecureRandom()**

The main key when using SecureRandom is to never use static seeds. SecureRandom seeds itself when initialized using good randomness, so, unless you really need to have a seed, it is safe to just initialize it without one. 

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
