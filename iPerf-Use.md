## 安装iPerf
```
#详细地址：https://iperf.fr/iperf-download.php
#  [Ubuntu / Debian / Mint]
sudo apt-get install iperf3
```

## iPerf 3 user documentation
```
https://iperf.fr/iperf-doc.php#3doc
```

## 启动
```
#服务端启动
sudo iperf3 -s -p 80
```
**注意：** -s指定为服务端，-p指定端口。服务器，80端口

```
#客户端启动
iperf3 -c serverIP -i 1 -t 50 -p 1234
```
**注意：**-c指定为客户端：服务端ip; -i指定为发送时间间隔：1s；-t指定发送数据数：发送50个数据包；-p指定服务端端口，1234端口