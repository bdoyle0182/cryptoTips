---
layout: page
title: Passwords
description: How to correctly and safely store user passwords, or derive keys from them
---

<script language="JavaScript">
function toggletext(cid)
{
 if ( document.getElementById(cid).style.display == "none" )
 {
   document.getElementById(cid).style.display = "block";
 }
 else
 {
   document.getElementById(cid).style.display = "none";
 };
}
</script>

**When Hashing Passwords**

Things to remember: 

* **Never** just hash the password with an existing hash function. **Always** use an existing password-based encryption scheme. 
* **Always** use PBKDF2, **NOT** PBKDF1
* **Always** use at least 1000 iterations in the key derivation algorithm
* **Always** use a random salt
  * Make sure the salt is at least 64 bits
  * Make sure that you use **SecureRandom** correctly (See our page on [**randomness**](SecureRandom.html))
  
  **Example:**


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
