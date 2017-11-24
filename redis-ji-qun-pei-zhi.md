# Redis 集群配置

* Redis 配置主从复制（读写分离）

  * 主服务器master\(处理写请求\)

    > \#　默认是绑定本机ip 127.0.0.1,如果要配置主从，就要绑定主机远程的IP  
    > bind 192.168.30.104
    >
    > ＃　如果需要密码，主从都要设置同样的密码
    >
    > requirepass "123456"

  * 从服务器slave\(处理读请求\)

    > ＃　绑定主服务器
    >
    > slaveof 192.168.30.104 6379
    >
    > ＃　配置密码
    >
    > requirepass "123456"
    >
    > masterauth "123456"

* Redis 配置sentinel\(集群的监控\) 

  * 编辑sentinel.conf

  ```
  # 后台模式运行
  daemonize yes
  ```

  ```
  port 26379
  # 绑定各自主机外网ip
  bind 192.168.2.210
  # 设置远程主机　需要两个sentinel主机同意，才能故障迁移
  sentinel monitor redis-master 192.168.2.210 6379 2
  # 主服务器在5000ｍ毫秒内没有返回ping信息，标记为sdown
  sentinel down-after-milliseconds redis-master 5000
  # 如果在180000毫秒中没有恢复，则认为是真正的怠机
  sentinel failover-timeout redis-master 180000
  # parallel-syncs选项指定了在执行故障转移时，最多可以有多少个从服务器同时对新的主服务器进行同步。这个数字越小，完成故障转移所需的时间就越长
  sentinel parallel-syncs redis-master 2
  # 主服务器的密码
  sentinel auth-pass redis-master 123456
  # 
  sentinel notification-script redis-master /etc/redis/notify.sh
  sentinel client-reconfig-script redis-master /etc/redis/failover.sh
  logfile /var/log/redis/redis-sentinel.log
  ```



