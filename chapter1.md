# Redis 配置



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



