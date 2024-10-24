# Fuzzing cheatsheet

## subdomain enummeration

```bash
ffuf -u http://10.129.227.227/ -H "Host: FUZZ.siteisup.htb" -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-words.txt -fc 200
```

## dirbusting

```bash
# dirsearch.py

dirsearch -u $URL -x 404 

# wfuzz

wfuzz -Z -c -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt   --hc 404 -u $URL #for file busting

wfuzz -Z -c -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directory.txt   --hc 404 -u $URL # for directory busting

export URL https://target/dir/FUZZ.php # replace php with desired filetype discovered 

wfuzz -Z -c -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-words.txt   --hc 404 -u $URL

#ffuf

ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt  -fc 404 -u http://target/fuzz

ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt -fc 404 -recursion -u http://target//FUZZ

```
