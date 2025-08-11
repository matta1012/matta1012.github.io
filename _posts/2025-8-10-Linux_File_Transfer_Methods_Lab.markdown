---
layout: post
title: HTB Linux File Transfer Methods Lab Report
date: 2025-8-10
categories: File_Tranfers
---

## Module: File Transfers
### Section: Linux File Transfer Methods

---
# HTB Linux File Transfer Methods Lab Report - File Transfer Report

#### Matthew Alvarez
#### August 8th, 2025
##### Module/Section: File Transfers / Linux File Transfer Method Lab 
---
# Objective
Utilizing Linux specific File Transfer Methods, attempt to answer the provided questions: 
- `Download the file flag.txt from the web root using Python from the Pwnbox. Submit the contents of the file as your answer.`
- `Upload the attached file named upload_nix.zip to the target using the method of your choice. Once uploaded, SSH to the box, extract the file, and run "hasher <extracted file>" from the command line. Submit the generated hash as your answer.`

---

# Tools and Environment
- Linux CMD Line
- SSH

---
# Target Information
- Target IP address: `10.129.9.138`

- Local Host: `Personal Virtual Box Kali Linux Machine`

- Scope: `Local Host & Target Machine`

---
# Write Up

Beginning this lab I look towards the first question which asks.
`Download the file flag.txt from the web root using Python from the Pwnbox. Submit the contents of the file as your answer.`

There is many ways to do this such as using `wget` & `curl`, however it specifically says to use Python. So quickly I recall that this module didn't talk specifically about python methods used to extract files from remote machines. So I did some research and discovered a simple script used to do so.

``` python
import urllib.request

url = "http://<ip address>/<directory+file>"
with urllib.request.urlopen(url, timeout=10) as r:
    body = r.read()
    open("flag.txt", "wb").write(body)
    print(body.decode(errors='replace').strip())
```

1. `import urllib.request` : Imports  `urllib.request` module from Python's library. This module allows you to open up URL's and web pages/files served over HTTP/HTTPS as well as send HTTP requests and handle responses.
2. `url = ""` : Defines the url of what you're accessing. In this case we are trying to download the `flag.txt` from the web root of the IP address.
3. `with urllib.request.urlopen(url, timeout=10) as r:` : Opens the URL. The `with . . . as r:` ensures that connection is properly closed after usage. `timeout` is 10 seconds when their isn't a response
4. `body = r.read()` : Reads the whole file at once, into memory as bytes and places this value into the variable `body`.
5. `open("flag.txt", "wb").write(body)` : Creates/opens a file called `flag.txt` in binary write mode, and writes the bytes into it from the `body` variable
6. `print(body.decode(errors='replace').strip())` : Prints out the information into the file. Converts the body into a string, replacing any invalid bytes to prevent errors. As well as remove any white spaces/newlines.

With this script we get the answer "`5d21cf3da9c0ccb94f709e2559f3ea50`"

Continuing on, question 2 requires us to : `Upload the attached file named upload_nix.zip to the target using the method of your choice. Once uploaded, SSH to the box, extract the file, and run "hasher <extracted file>" from the command line. Submit the generated hash as your answer.`

The first method that comes to mind is the SSH Upload Operation. Basically we utilize scp (Secure Copy) to upload files to a remote host. A caveat though, is that the remote company must have out bound connections enabled/allowed. Luckily the HTB host does allow for out bound connections.

So quickly we can transfer this .zip to the remote host with the command
`scp upload_nix.zip htb-student@<ip address>:<local directory>`

![](/assets/images/Pasted_image_20250810103251.png) 

![](/assets/images/Pasted_image_20250810103255.png)

 Now we are required to extract the file, which we could have done before we transferred the .zip. However HTB specifically wants us to do it after we have delivered it to the remote host.

On the remote host, since there's no `unzip` installed or other unzipping software, I decided to create a python script to extract the contents.

The script:
```python
import zipfile

with zipfile.ZipFile('upload_nix.zip', 'r') as zip_ref:
	zip_ref.extractall('')
```

`import zipfile` : imports the built in python ZipFile module that allows you to read `.zip` files

`zipfile.ZipFile('upload_nix.zip', 'r')` : Opens the specified zip file in read mode

`with . . . as zip_ref` : A context manager that ensures the ZIP file is closed when the script is run

`zip_ref.extractall('')` : Extracts all content from the ZIP to the current working directory

![](/assets/images/Pasted_image_20250810104639.png)

After extracting the `.zip` we are left with a `.txt` file that contains a string of characters/numbers 
![](/assets/images/Pasted_image_20250810105053.png)

HTB specifically wants us to run hasher against this file/string. When we do that we receive back our answer

![](/assets/images/Pasted_image_20250810105140.png)