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
  
  
<a href="javascript:toggletext('mytext')"> <b> Why? </b></a> 

<div id="mytext" style="display: none;"> 

<p> Using established standards is nearly always bettern than implementing something yourself. Be safe and use existing implementations! </p>
 
 <p>PBKDF1 was designed for much smaller key lengths than are used in modern times, so should generally be avoided. </p>
 
 <p>1000 iterations is the minimum value suggested for the current password based encryption standard. </p>
 
<p>It's important to keep a user's data private. This is important for trust by the user in the company/software you've created to protect their data.
If you do not hash passwords, and your passord table is hacked; then your entire userbase or users in the file have just had their passwords stolen
in the clear. If you hash their passwords, that at least adds at least a layer of protection. However, it is still not enough because there are dictionary
attacks out there such as CrackStation, which are estimated to have 50% of all human generated passwords in its dictionary. So adding a salt is therefore
incredibly important to reduce vunlerability. By adding a salt, you prevent the hacker from being able to preprocess their dictionary into a table of password
and hashes because the salts are used in the hash to verify a user's password in your application. This adds several orders of magnitude in difficulty to 
the attack. Unfortunately public salts are also hackable so having a one time private salt with a small key space that is not stored is important too. By 
doing this, you are adding up to the size of the private salt key space hashes in order to verify a user. But for each user, this will only be around a 
hundredth of a second in computation time. When doing this with a dictionary of a billion passwords, this adds a decent amount of time to their attack.
Finally, its important to use a password based key derivation function (PBKDF2) rather than just plain SHA256 in order to protect weak passwords by slowing down the hash process, which will again slow down the hacker's attack. </p>

 </div> 



  
  
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
