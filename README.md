<p align="center">
  <h1 align="center"> TCP proxy </h1>
	<p align="center">For cybersecurity professionals and educational purposes only!</p>
	<p align="center">Use only on hosts/networks you own or have permission to test!</p>
</p>

## 🚀 Quick Start
## 1. Modify TCP-client's requests, editing the script like this:
```python
def request_handler(buffer):
    old_host = b'192.168.204.139'
    new_host = b'www.example.com'
    modified_buffer = buffer.replace(old_host, new_host)
    if modified_buffer != buffer: 
        print("[!!!] HOST HEADER MODIFIED by proxy")
    return modified_buffer
```
### 1.1 Doing so, we modify this line's content of [tcp-client.py](<https://github.com/Zauzanov/tcp-client/blob/main/tcp-client.py>) file, replacing the initial request:
```python
# send some data in the form of bytes
client.send(b"GET / HTTP/1.1\r\nHost: 192.168.204.129\r\n\r\n")
```
### 1.2 Run the proxy:
```bash
python proxy.py 192.168.204.139 9995 192.168.204.139 9995 True
```
### 1.3 Run the tcp_client, editing IP/Port:
```bash
python tcp_client.py
```
### 1.4 And the output is:
```bash
[!!!] HOST HEADER MODIFIED by proxy
[==>] Dumping Modified Request:
0000 47 45 54 20 2F 20 48 54 54 50 2F 31 2E 31 0D 0A  GET / HTTP/1.1..
0010 48 6F 73 74 3A 20 77 77 77 2E 65 78 61 6D 70 6C  Host: www.exampl
0020 65 2E 63 6F 6D 0D 0A 0D 0A                       e.com....
[==>] Sent to remote.
```

### The reason I needed to create a new branch for this functionality: after all the manipulations, the output was the same:
```bash
[==>]Received 41 bytes from localhost.
0000 47 45 54 20 2F 20 48 54 54 50 2F 31 2E 31 0D 0A  GET / HTTP/1.1..
0010 48 6F 73 74 3A 20 31 39 32 2E 31 36 38 2E 32 30  Host: 192.168.20
0020 34 2E 31 33 39 0D 0A 0D 0A                       4.139....
[!!!] HOST HEADER MODIFIED by proxy
[==>] Sent to remote.
```
The problem wasn't that the modification didn't happen, but that the hexdump function was printing the original buffer just before the modified buffer is sent.
