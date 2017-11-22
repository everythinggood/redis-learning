# Redis 基础配置

* > 某些命令需要安装redis-tools  
  > ubuntu:
  >
  > sudo apt-get install redis-tools
* 数据持久化配置

  * RDB\(默认开启\)

    > save \[second\] \[changes\]
    >
    > 在second秒内发生了多少次change，就进行一次写入硬盘rdb文件的行为，这个行为是由子进程来完成的。

    ```
    save 900 1
    save 300 10
    save 60 10000

    ##手动触发
    bgsave
    ```

  * AOF\(默认关闭\)

    ```
    ## 开启AOF redis操作写入日志文件
    appendonly yes

    # 同步操作到日志时刻的设置
    ## 将同步操作交给系统决定，IO什么时候有空就进行同步操作，速度最快，但最不安全
    appendfsync no
    ## 每一次操作就进行一次同步操作，速度最慢，但数据是最安全的
    appendfsync always
    ## 每一秒钟就进行一次同步操作，适中做法(推荐)
    appendfsync evervsec

    # AOF文件重写
    ## 场景：有多条ｉｎｃｒ操作，但是可以整合成一条ｓｅｔ命令解决
    ## 执行 BGREWRITEAOF 命令， Redis 将生成一个新的 AOF 文件， 这个文件包含重建当前数据集所需的最少命令。
    ## Redis 2.2 需要自己手动执行 BGREWRITEAOF 命令；
    ## Redis 2.4 则可以自动触发 AOF 重写， 具体信息请查看 2.4 的示例配置文件。
    bgrewriteaof

    #AOF文件出错(corrupt)
    ## 1.备份aof文件
    ## 2.修复aof文件
    redis-check-aof -fix
    ## 3.比对两者aof文件的区别（使用linux\uninx命令进行）-->可选
    diff -u [old] [new] > [update.patch]
    ## 4.重启redis，重新加载修复过的aof文件
    ```

> #### 数据库一定要做容灾备份

* 内存管理和数据淘汰机制

  * 最大内存设置

    > 默认下，redis在32位os中最大使用3G内存,在64位os中无限制  
    > 为防止内存使用过度，影响其他服务的执行，我们要设定redis的最大内存

    ```
    maxmemory 100mb
    ```

  * 数据淘汰机制

    > LRU算法：淘汰上次使用最早的，并使用次数最少的key

    * volatile-lru

    > 使用lru算法，并只淘汰设置有效期的key

    * allkeys-lru

    > 使用lru算法，淘汰所有满足算法条件的key

    * volatile-random

    > 随机淘汰设置了有效期的key

    * allkeys-random

    > 随机淘汰key

    * volatile-ttl

    > 淘汰有效期最短的key

    ```
    #　默认是noeviction，即不进行数据淘汰
    maxmemory-policy volatile-lru
    ```

> redis算法的时间复杂度基本在O\(1\)~O\(N\)之间，一般使用大量的O\(N\)命令会导致时间延迟，因为redis是线程串行运行的

* 记录耗时时间长的命令　的配置
  ```
  showlog-log-slower-than xxxms #执行时间慢于xxx毫秒的命令会写入Show Log
  showlog-max-len xxx #Show Log的长度，即最大多少条记录
  ```

* showLog　命令
  ```
  showlog get [number] #获取showlog第number条记录
  showlog reset #重置showlog
  ```





