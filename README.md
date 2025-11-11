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
## 3. From Metasploitable2 Terminal(it has FTP-client and server as well. Creds: user:user):
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

