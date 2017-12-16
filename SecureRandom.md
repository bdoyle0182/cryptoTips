---
layout: page
title: Hashes
description: How to avoid using insecure hash functions
---

**When Using Hash Functions**

The main thing to remember is that some commonly used hash functions are actually **not secure**. The ones to avoid are:

* MD5
* SHA1

Other common hash functions, like SHA3 or SHA256, are safe

Be careful when **truncating**! For most standard hash functions a reasonable amount of truncation is safe. Be wary though, the more you truncate your hash, the weaker your protocol becomes. So, truncate if you must, but keep it within reason. 

