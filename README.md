<p align="center">
  <h1 align="center"> TCP proxy </h1>
	<p align="center">For cybersecurity professionals and educational purposes only!</p>
	<p align="center">Use only on hosts/networks you own or have permission to test!</p>
</p>

## 🚀 Quick Start
## 1. Download 'proxy.py' file and run it like this to open the helper:
```bash
python proxy.py --help  
```
## 2. Run:
```bash
sudo python proxy.py 192.168.1.203 21 192.168.1.204 21 True
```
## 3. From Metasploitable2(intentionally vulnerable Linux virtual machine) Terminal(it has FTP-client and server as well. Creds: user:user):
```bash
ftp 192.168.1.203
```
Now you can see that it works as a traffic forwarder that simply passes the FTP client's connection attempt to a real FTP server running on the [remotehost]:[remoteport].

## 4. Or you can do it downloading [tcp-client.py](<https://github.com/Zauzanov/tcp-client/blob/main/tcp-client.py>) file from my previous project(don't forget to edit IP-address and port):
```bash
sudo python proxy.py [localhost] [localport] [remotehost] [remoteport] True
```
Terminal 2: 
```bash
python tcp-client.py
```
OUTPUT:
```bash
[==>]Received 41 bytes from localhost.
0000 47 45 54 20 2F 20 48 54 54 50 2F 31 2E 31 0D 0A  GET / HTTP/1.1..
0010 48 6F 73 74 3A 20 31 39 32 2E 31 36 38 2E 32 30  Host: 192.168.xx
0020 34 2E 31 33 39 0D 0A 0D 0A                       x.xxx....
[==>] Sent to remote.
[==>]Received 41 bytes from localhost.
0000 47 45 54 20 2F 20 48 54 54 50 2F 31 2E 31 0D 0A  GET / HTTP/1.1..
0010 48 6F 73 74 3A 20 31 39 32 2E 31 36 38 2E 32 30  Host: 192.168.xx
0020 34 2E 31 33 39 0D 0A 0D 0A                       x.xxx....
[==>] Sent to remote.

```

## 5. IP Masking: how to check whether the script masks your IP, replacing it with the proxy's own IP address, or not:

### 5.1 Run the proxy on your Kali machine(Proxy host): 
```bash
sudo python proxy.py 192.168.204.139 21 192.168.204.129 21 True
```

### 5.2 On Metasploitable2 machine - Client host (192.168.204.129): 
```bash
ftp 192.168.204.139 			# Proxy's IP address in my case
```
### 5.3 At the same time Metasploitable2's FTP server is your Destination server(Remote host) in this case;

### 5.4 After the interaction, let's check the FTP server's logs on the Metasploitable2 machine:
``` bash
sudo tail /var/log/vsftpd.log
sudo cat /var/log/vsftpd.log
``` 

![Logs](https://raw.githubusercontent.com/Zauzanov/anonymat/refs/heads/main/IP_masking.png)

If you see your Kali machine's IP address, you're good - it means the script effectively "masks" the original IP by replacing it. 


## 6. How to test content changing in requests for debugging purposes(For cybersecurity professionals and educational purposes only! Use only on hosts/networks you own or have permission to test!): 
### 6.1 We have to edit the script's function like this(Line 51):
BEFORE:
```python
def request_handler(buffer):
# modify the packet
	return buffer
```

AFTER: 
```python
def request_handler(buffer):
    # Example: Replace the login 'USER badguy' with 'USER user'. 
    modified_buffer = buffer.replace(b'USER badguy', b'USER user')
    return modified_buffer
```
### 6.2. I deliberately input a wrong login(user 'badguy' doesn't exist at all) to demonstrate the content changing process:
![Login](https://raw.githubusercontent.com/Zauzanov/anonymat/refs/heads/main/bad_login.png)

### 6.3 The script replaces the login in a discreet way and accepts 'user' instead of 'badguy' login:
```bash
sudo python proxy.py 192.168.204.139 21 192.168.204.129 21 True
[*] Listening 192.168.204.139:21
> Received incoming connection from 192.168.204.129:34688
0000 32 32 30 20 28 76 73 46 54 50 64 20 32 2E 33 2E  220 (vsFTPd 2.3.
0010 34 29 0D 0A                                      4)..
[<==] Sending 20 bytes to localhost.
[==>]Received 13 bytes from localhost.
0000 55 53 45 52 20 62 61 64 67 75 79 0D 0A           USER badguy..
[==>] Sent to remote.
[<==] Received 34 bytes from remote.
0000 33 33 31 20 50 6C 65 61 73 65 20 73 70 65 63 69  331 Please speci
0010 66 79 20 74 68 65 20 70 61 73 73 77 6F 72 64 2E  fy the password.
0020 0D 0A                                            ..
[<==] Sent to localhost.
[==>]Received 11 bytes from localhost.
0000 50 41 53 53 20 75 73 65 72 0D 0A                 PASS user..
[==>] Sent to remote.
[<==] Received 23 bytes from remote.
0000 32 33 30 20 4C 6F 67 69 6E 20 73 75 63 63 65 73  230 Login succes
0010 73 66 75 6C 2E 0D 0A                             sful...
```
### 6.4 Then we input password(creds are user:user for Metasploitable2 FTP server).
That's it. 

So you can use this modification as a starting point for your tests, labs and cybersecurity research:
```python
def request_handler(buffer):
    # Example: Replace 'USER validuser' with 'USER intruder'
    modified_buffer = buffer.replace(b'USER validuser', b'USER intruder')
    return modified_buffer
```
## 7. To test if you can replace the server's response using response_handler(buffer), you need this kind of construction:
```python
def response_handler(buffer):
    old_response_start = b'220 '
    new_response = b'220 SERVER IS COMPROMISED!' 
    modified_buffer = buffer.replace(old_response_start, new_response)
    if modified_buffer != buffer:
        print(f"[!!!] RESPONSE MODIFIED: Altered server banner")
    return modified_buffer
```
### 7.1 Then run the proxy: 
```bash
sudo python proxy.py 192.168.204.139 21 192.168.204.129 21 True
```

### 7.2 as a FTP-client from Metaspoitable2 Terminal:
```bash
ftp 192.168.204.139
```
### 7.3 Server's response on Kali:
```bash
[*] Listening 192.168.204.139:21
> Received incoming connection from 192.168.204.129:56474
0000 32 32 30 20 28 76 73 46 54 50 64 20 32 2E 33 2E  220 (vsFTPd 2.3.
0010 34 29 0D 0A                                      4)..
[!!!] RESPONSE MODIFIED: Altered server banner
[<==] Sending 42 bytes to localhost.
[*] No more data. Closing connections.
```

### 7.4 On FTP-client's side:
![Banner](https://raw.githubusercontent.com/Zauzanov/anonymat/refs/heads/main/altered_banner.png)
