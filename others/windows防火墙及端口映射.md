1、windows开启端口

netsh firewall add portopening TCP 8888 MyWebPort

2、删除端口

netsh firewall delete portopening protocol=TCP port=3389

3.添加端口映射

netsh interface portproxy add v4tov4 listenaddress=172.18.59.170 listenport=6666 connectaddress=172.16.1.58  connectport=6666

---取消上面配置的端口转发，可以用如下语句：

netsh interface portproxy delete v4tov4 listenaddress=192.168.1.8  listenport=33891
---如果想查看已经配置了哪些端口转发，可以用如下语句：

netsh interface portproxy show  v4tov4



侦听 ipv4:                 连接到 ipv4:

地址            端口        地址            端口
--------------- ----------  --------------- ----------
172.18.59.170   22222       172.16.1.58     22
172.18.59.170   6666        172.16.1.58     6666
172.18.59.170   80          172.16.1.58     8080