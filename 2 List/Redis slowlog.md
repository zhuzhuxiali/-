# Redis slowlog

Redis：远程字典服务器，开源、基于内存、高性能、Key-Value型NoSQL数据库，数据主要放内存，当电脑重启会丢失数据
Redis Slowlog(慢查询日志)是Redis内置的性能诊断工具，专门记录执行时间超过指定阈值的命令，快速定位导致Redis变慢的元凶

和MySQL区别：
*MySQL存底层数据+Redis做热点缓存，提升系统速度

redis中的slowlog使用链表来保存


```
struct redisServer {

    // ...


    // 下一条慢查询日志的 ID
    long long slowlog_entry_id;


    // 保存了所有慢查询日志的链表
    list *slowlog;


    // 服务器配置 slowlog-log-slower-than 选项的值
    long long slowlog_log_slower_than;


    // 服务器配置 slowlog-max-len 选项的值
    unsigned long slowlog_max_len;//unsigned无符号，代表只能存非负数


    // ...

};
```


一条slowlog entry标识

```
//typedef是类型别名关键词
typedef struct slowlogEntry {


    // 唯一标识符
    long long id;


    // 命令执行时的时间，格式为 UNIX 时间戳
    time_t time;


    // 执行命令消耗的时间，以微秒为单位
    long long duration;


    // 命令与命令参数
    robj **argv;


    // 命令与命令参数的数量
    int argc;


} slowlogEntry;//struct的类型别名：slowlogEntry

```



