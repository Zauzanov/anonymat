<p align="center">
  <h1 align="center"> TCP proxy </h1>
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

If you see your Kali machine's IP address, you're good - it means the script effectively "masks" the original IP by replacing it. 




