# PM2

pm2有了解么，集群是启动到多个进程上还是线程上

cluster是fork的派生，cluster支持所有cluster拥有的特性；

fork不支持socket地址端口复用，cluster支持地址端口复用。因为只有node的cluster模块支持socket选项SO\_REUSEADDR；

