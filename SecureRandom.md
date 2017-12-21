---
layout: page
title: Hashes
description: How to avoid using insecure hash functions
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


**When Using Hash Functions**

The main thing to remember is that some commonly used hash functions are actually **not secure**. The ones to avoid are:

* MD5
* SHA1

Other common hash functions, like SHA3 or SHA256, are safe

Be careful when **truncating**! For most standard hash functions a reasonable amount of truncation is safe. Be wary though, the more you truncate your hash, the weaker your protocol becomes. So, truncate if you must, but keep it within reason. 

<a href="javascript:toggletext('mytext')"> <b> Why? </b></a> 

<div id="mytext" style="display: none;"> Modern GPU's can compute millions of MD5 and SHA1 hashes every second. Whereas SHA256 can only compute tens of thousands hashes every second based on current 
GPU's. There are several other reasons you should be using SHA256, like SHA1's smaller key space adding to it's vulnerability. The fact that a hacker could rent a GPU cluster on AWS for a couple bucks to crack passwords hashed with SHA1 should be reason enough not to use it.

</div>
