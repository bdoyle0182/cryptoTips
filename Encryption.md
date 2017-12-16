---
layout: page
title: Encryption
description: Everything on how to correctly use the Cipher API
---


Things to remember: 
* The current standard for ciphers is **AES**, so unless you've done enough research to determine that a different cipher is going to be better for your application, that is what we suggest using. 
 * When in doubt, stick with **AES**. Java supports many block ciphers, some of which (like **DES**) are **not** secure. 
* **Never** use **ECB** mode. Watch out! This is the default mode when using **AES**. 
* **Never** use the same IV (initialization vector) for each encryption. Use a new **random** IV each time. We will present an example of using a random IV with **CBC** mode. 
 * Make sure you use **SecureRandom** correctly (See our page on [**randomness**](SecureRandom.html))
* **Never** hard code your encryption keys, use the built in methods for generating keys instead. 

**Why?**
In **ECB** mod, identical blocks of the message get encrypted to identical blocks of cipher-text. This means adversarial actors gain some amount of information about the message. Other modes (like **CBC**) don't reveal **any** information about the message, so it is must safer to just use those. 

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

**Advanced Note**: While **CBC** mode is the most common, **CTR** is just as secure, and has the nice property of being pre-computable! Consider using **CTR** in place of **CBC**. Make sure you do good research before so you can implement everything correctly, and make sure you use a **random** IV!

**Advanced Note**: A random IV is not required if the first block of a message is filled with random data and discarded during decryption. While IVs are a good practice in general, the first block randomness construction may be better for your implementation. Make sure you've done enough research before you attempt to implement a protocol this way!
