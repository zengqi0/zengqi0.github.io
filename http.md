![image-20230801092651602](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230801092651602.png)

![image-20230801092941190](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230801092941190.png)

![image-20230801093009961](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230801093009961.png)

![image-20230801093126545](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230801093126545.png)

浏览器有没有对应dns相关信息的缓存

没有的话去本地的host文件  没有配置

dns请求   应用层会调用传输层的soket api

传输层会在 dns报文前加一个udp请求头 再往下网络层加一个ip请求头  

再往下  二层寻址 mac头 对应请求报文交给下一个机器的mac地址

mac地址是网络层的arp请求去对应ip地址的mac地址 

 再通过物理介质  路由器上面 ，然后从物理层到数据链路层

