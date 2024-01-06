***Take Aways***

*Source Code Review*

  *Obfusication*

  *SSDLC*

  *IDOR*

  *Security Misconfiguration*

  *Command Injection*

  **Source Code Review**

Upon viewing the source code of the Landing page we find 

![image](https://github.com/reneanto/write-ups/assets/44943249/f27d8136-d8a0-4460-a3ed-145732ffe00f)

and upon checking the custom.js file, we find the JS being obfusciated in Hexadecimal. Cyberchef ftw ofc.

The landing page source code has comments which indicates the presence of the directory /logs

![image](https://github.com/reneanto/write-ups/assets/44943249/3360284f-ab5f-47bb-a15c-fbc26bd3068c)

The text file further gives us a lead with an interesting question and  a key to login to a specific endpoint

![image](https://github.com/reneanto/write-ups/assets/44943249/d91f9d36-b904-4f81-a19d-ddbda16fe6c5)

refer https://www.hackerone.com/knowledge-center/what-ssdlc-secure-software-development-life-cycle

or 

Directory Busting ftw

![image](https://github.com/reneanto/write-ups/assets/44943249/bfd669f7-5b1f-405b-b375-e722de84692a)

Upon Visiting this endpoint it gives another lead to a specific call which can be further abused via the IDOR or BOLA interm of APIs

The IDOR vulnerability gives us the details of the administrative endpoint along with the admin credentials

Logging into the admin endpoint gives us a functionality of command injection directly.

With this we can list the files and take a look at the same and figure out which file was modified by the attacker.

The modified file's source code gives us the credentials to login to the same and finally we can take a look at the index.php's original source code and get the final flag.
