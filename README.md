# TryHackMe-The_Cod_Caper-WriteUp
My solution for the room called "The Cod Craper" in TryHackMe

## Host Enumeration 

<p> The first step is to see what ports and services are running on the target machine. 
Recommended Tool - nmap:
Useful flags: -p -sC -A </p>

<p> To answer this questions we need to scan the machine with nmap and analyze the results</p>

```nmap -A IP -vv
```


**Question 1:** How many ports are open on the target machine? <br>
<code> 2 </code>

**Question 2:** What is the http-title of the web server? <br>
<code> Apache2 Ubuntu Default Page: It works </code>

**Question 3:** What version is the ssh service? <br>
<code> OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 </code>

**Question 4:** What is the version of the web server? <br>
<code> Apache/2.4.18 </code>

## Web Enumeration 

<p> Since the only services running are SSH and Apache, it is safe to assume that we should check out the web server first for possible vulnerabilities. One of the first things to do is to see what pages are available to access on the web server.
Recommended tool: gobuster
Useful flags: -x --url --wordlist 
	
Recommended wordlist: big.txt </p>

**Question 1 :** What is the name of the important file on the server? <br>
<code> administrator.php </code>

