---
layout: page
title: Tips
description: What we have so far.
---

## Our Encryption Guide:

**When using Ciphers (i.e. anything to do with the Cipher API)**

Things to remember: 
* The current standard for ciphers is AES, so unless you've done enough research to determine that a different cipher is going to be better for your application, that is what we suggest using. 
 * When in doubt, stick with AES. Java supports many block ciphers, some of which (like DES) are **not** secure. 
* Never use ECB mode. Watch out! This is the default mode when using AES. 
* Never use the same IV (initialization vector) for each encryption. Use a new random IV each time. We will present an example of using a random IV with CBC mode. 
 * Make sure you use SecureRandom correctly (link to secure random section)
* Don't hard code your encryption keys, use the built in methods for generating keys instead. 

**Example:**

```java
import java.security.SecureRandom;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.*;

...

String text = "encrypt me";

//initialize the SecureRandom
SecureRandom ranGen = new SecureRandom(); //Important! Don't use a static seed

//setup the IV
int ivSize = 16; 
byte[] iv = new byte[ivSize]; 
ranGen.nextBytes(iv); 
IvParameterSpec ivParameterSpec = new IvParameterSpec(iv); //Important! If you don't use a random IV your encryption is NOT SECURE

//Get the key
KeyGenerator kgen = KeyGenerator.getInstance("AES"); 
kgen.init(128); 
SecretKey key = kgen.generateKey(); 

//Setup the cipher
Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding"); //Important! Make sure you specify CBC mode, otherwise it defaults to ECB
cipher.init(Cipher.ENCRYPT_MODE, key, ivParameterSpec); 

//Encrypt the plaintext
byte[] plaintext = text.getBytes("UTF-8"); 
byte[] ciphertext = cipher.doFinal(plaintext); 



```

Advanced Note: While CBC mode is the most common, CTR is just as secure, and has the nice property of being precomputable! Consider using CTR in place of CBC. Make sure you do good research before to make sure you implement everything correctly, and make sure you use a random IV!

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

**When Hasing Passwords**

Things to remember: 

* Always use PBKDF2, **NOT** PBKDF1
* Always use at least 1000 iterations in the key derivation algorithm
* Always use a random salt
  * Make sure the salt is at least 64 bits
  * Make sure that you use SecureRandom correctly (link to SecureRandom section)
  
  **Example**


```java
  
String password = "myPassword";
String plaintext = "some text"; 
int saltLength = 64;
int iterations = 10000;
int keyLength = 256; 

//Setup salt 
//Important! If you don't use a salt this won't be secure!
SecureRandom random = new SecureRandom(); 
byte[] salt = new byte[saltLength];
random.nextBytes(salt);

//Setup the KD algorithm
SecretKeyFactory factory = SecretKeyFactory.getInstance("PBKDF2WithHmacSHA256");
KeySpec spec = new PBEKeySpec(password.toCharArray(), salt, iterations, keyLength);
SecretKey tmp = factory.generateSecret(spec);
SecretKey secret = new SecretKeySpec(tmp.getEncoded(), "AES");

//Setup our cipher
Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");

//Setup the iv
//Important! Make sure you use a random IV!
byte[] iv = new byte[cipher.getBlockSize()]; 
random.nextBytes(iv); 
IvParameterSpec ivParams = new IvParameterSpec(iv);

cipher.init(Cipher.ENCRYPT_MODE, secret, ivParams);

byte[] ciphertext = cipher.doFinal(plaintext.getBytes("UTF-8"));
  
```

**When Using Hash Functions**

The main thing to remember is that some commonly used hash functions are actually **not secure**. The ones to avoid are

* MD5
* SHA1

Other common hash functions, like SHA3 or SHA256, are safe