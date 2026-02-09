# 📚 Redis 命令分类速查手册（基于 Redis 7.2 整理）

> ⚠️ **重要提示**  
> - 命令随 Redis 版本迭代可能增删改（如 `BRPOPLPUSH` 已废弃，推荐 `LMOVE`）  
> - **生产环境务必以 [Redis 官方命令文档](https://redis.io/commands/) 为准**  
> - 本文档适合离线速查，建议收藏 + 搭配官方文档使用  
> - 模块命令（如 RedisJSON/RediSearch）未包含，需查阅对应模块文档  

---

## 🔑 目录导航
| 分类                                  | 说明                     | 核心场景               |
| ------------------------------------- | ------------------------ | ---------------------- |
| [Keys](#keys-键操作)                  | 键生命周期管理           | 删除/过期/扫描键       |
| [Strings](#strings-字符串)            | 二进制安全字符串         | 缓存/计数器/位操作     |
| [Hashes](#hashes-哈希)                | 字段-值映射              | 对象存储（如用户资料） |
| [Lists](#lists-列表)                  | 双向链表                 | 消息队列/最新列表      |
| [Sets](#sets-集合)                    | 无序唯一集合             | 标签/好友关系          |
| [Sorted Sets](#sorted-sets-有序集合)  | 按分数排序集合           | 排行榜/延迟队列        |
| [Streams](#streams-流)                | 持久化消息流             | 事件溯源/日志          |
| [Pub/Sub](#pubsub-发布订阅)           | 消息广播                 | 实时通知               |
| [Transactions](#transactions-事务)    | 命令打包执行             | 原子操作               |
| [Scripting](#scripting-lua-脚本)      | Lua 脚本执行             | 复杂逻辑原子化         |
| [Functions](#functions-函数-redis-70) | 服务端函数（Redis 7.0+） | 替代部分脚本场景       |
| [Connection](#connection-连接)        | 客户端连接管理           | 认证/协议切换          |
| [Server](#server-服务器)              | 服务监控与配置           | 运维/诊断              |
| [Cluster](#cluster-集群)              | 集群运维命令             | 分布式部署             |
| [HyperLogLog](#hyperloglog)           | 基数估算                 | UV 统计                |
| [Geospatial](#geospatial-地理空间)    | 位置索引                 | LBS 应用               |
| [Bitmaps](#bitmaps-位图)              | 位级操作                 | 签到/布隆过滤器        |

---

## 🔑 Keys（键操作）
```text
DEL          # 删除键（同步）
UNLINK       # 异步删除键（推荐大键使用）
EXISTS       # 检查键是否存在
TYPE         # 返回键的数据类型
TTL / PTTL   # 剩余过期时间（秒/毫秒）
EXPIRE / PEXPIRE / EXPIREAT / PEXPIREAT  # 设置过期
PERSIST      # 移除过期时间
RENAME / RENAMENX  # 重命名
SCAN         # 渐进式遍历键（替代 KEYS！）
KEYS         # 模糊查询键（⚠️ 生产禁用！阻塞风险）
DUMP / RESTORE  # 序列化/反序列化
RANDOMKEY    # 随机返回一个键
MOVE         # 移动键到其他数据库
SORT         # 排序（支持外部 key）
WAIT         # 等待副本同步确认
```

## 🔤 Strings（字符串）
```text
SET / GET / MSET / MGET       # 基础读写
SETNX / SETEX / PSETEX        # 条件/带过期设置
GETEX / GETDEL                # 获取时操作（Redis 6.2+）
INCR / DECR / INCRBY / DECRBY # 整数原子增减
INCRBYFLOAT                   # 浮点数增减
APPEND / STRLEN               # 追加/长度
GETRANGE / SETRANGE           # 子串操作
BITCOUNT / BITPOS / BITOP     # 位图基础（详见 Bitmaps）
BITFIELD                      # 高级位域操作
STRALGO LCS                   # 计算最长公共子序列（Redis 6.0+）
```

## 🗂️ Hashes（哈希）
```text
HSET / HSETNX / HGET / HDEL   # 字段操作
HMSET (deprecated) → 用 HSET  # 批量设置（HSET 支持多字段）
HMGET / HGETALL               # 批量获取
HINCRBY / HINCRBYFLOAT        # 字段值增减
HKEYS / HVALS / HLEN          # 元数据查询
HEXISTS                       # 检查字段存在
HSCAN                         # 渐进式遍历字段
HRANDFIELD                    # 随机返回字段（Redis 6.2+）
HSTRLEN                       # 获取字段值长度
```

## 📋 Lists（列表）
```text
LPUSH / RPUSH / LPOP / RPOP   # 头尾插入弹出
LPUSHX / RPUSHX               # 仅当列表存在时操作
LINDEX / LLEN / LRANGE        # 索引/长度/范围查询
LINSERT                       # 指定元素前后插入
LREM / LSET / LTRIM           # 删除/修改/修剪
LMOVE                         # 原子转移元素（替代 RPOPLPUSH）
BLPOP / BRPOP                 # 阻塞式弹出（消息队列核心）
BLMOVE                        # 阻塞式转移（Redis 6.2+）
LMPOP / BLMPOP                # 弹出多个列表元素（Redis 7.0+）
LPOS                          # 查找元素位置（Redis 6.0+）
```

## 🌐 Sets（集合）
```text
SADD / SREM                   # 添加/删除成员
SCARD / SMEMBERS              # 基数/全量成员
SISMEMBER / SMISMEMBER        # 单/批量存在检查
SINTER / SUNION / SDIFF       # 交/并/差集
SINTERSTORE / SUNIONSTORE / SDIFFSTORE  # 结果存新集合
SRANDMEMBER / SPOP            # 随机获取/弹出
SSCAN                         # 渐进式遍历
```

## 📊 Sorted Sets（有序集合）
```text
ZADD                          # 添加成员（支持 NX/XX/CH 等选项）
ZREM / ZCARD / ZSCORE         # 删除/计数/查分
ZRANGE / ZREVRANGE            # 按排名范围（支持 WITHSCORES）
ZRANGEBYSCORE / ZREVRANGEBYSCORE  # 按分数范围
ZRANGEBYLEX / ZREVRANGEBYLEX  # 按字典序范围
ZRANK / ZREVRANK              # 查询排名
ZINCRBY                       # 增量更新分数
ZPOPMIN / ZPOPMAX             # 弹出最小/最大分成员
BZPOPMIN / BZPOPMAX           # 阻塞式弹出
ZMPOP / BZMPOP                # 弹出多个成员（Redis 7.0+）
ZUNION / ZINTER / ZDIFF       # 并/交/差集（Redis 6.2+）
ZUNIONSTORE / ZINTERSTORE / ZDIFFSTORE  # 结果存储
ZMSCORE                       # 批量查分
ZSCAN                         # 渐进式遍历
```

## 🌊 Streams（流｜Redis 5.0+）
```text
XADD                          # 追加消息（支持 MAXLEN 修剪）
XREAD / XREADGROUP            # 读取（支持阻塞 BLOCK）
XRANGE / XREVRANGE            # 按 ID 范围查询
XLEN                          # 流长度
XDEL                          # 删除消息
XTRIM                         # 修剪流
XGROUP                        # 消费者组管理（CREATE/DESTROY/SETID）
XACK / XCLAIM / XPENDING      # 消息确认与转移
XAUTOCLAIM                    # 自动认领滞留消息（Redis 6.2+）
XINFO                         # 查看流/组元数据
```

## 📡 Pub/Sub（发布订阅）
```text
PUBLISH                       # 发布消息到频道
SUBSCRIBE / UNSUBSCRIBE       # 订阅/退订频道
PSUBSCRIBE / PUNSUBSCRIBE     # 模式订阅（如 news.*）
PUBSUB CHANNELS / NUMSUB / NUMPAT  # 查询订阅状态
```

## 🔄 Transactions（事务）
```text
MULTI                         # 标记事务开始
EXEC                          # 执行队列命令
DISCARD                       # 放弃事务
WATCH / UNWATCH               # 乐观锁监控键
```

## 🧠 Scripting（Lua 脚本）
```text
EVAL / EVALSHA                # 执行脚本
SCRIPT LOAD / EXISTS / FLUSH  # 脚本缓存管理
SCRIPT KILL                   # 终止长时间脚本
SCRIPT DEBUG                  # 调试模式
```

## 🧩 Functions（函数｜Redis 7.0+）
```text
FCALL / FCALL_RO              # 调用函数（RO=只读）
FUNCTION LOAD / DELETE / LIST # 函数库管理
FUNCTION DUMP / RESTORE       # 导出/导入函数
FUNCTION FLUSH / STATS / KILL # 清空/统计/终止
```

## 🔌 Connection（连接）
```text
AUTH                          # 密码认证
HELLO                         # 协议切换（RESP3，Redis 6.0+）
PING / ECHO                   # 心跳/回显
SELECT                        # 切换数据库（0-15）
CLIENT SETNAME / GETNAME      # 设置/获取连接名
CLIENT ID / LIST / KILL       # 连接管理
QUIT                          # 关闭连接
READONLY / READWRITE          # 集群模式读写控制
RESET                         # 重置连接状态（Redis 6.2+）
```

## 🖥️ Server（服务器）
```text
INFO                          # 服务器全量信息
CONFIG GET / SET / REWRITE    # 配置管理
ACL *                         # 访问控制（CAT/LIST/SETUSER... Redis 6.0+）
BGSAVE / SAVE                 # 持久化触发
BGREWRITEAOF                  # AOF 重写
FLUSHDB / FLUSHALL            # 清空数据库（慎用！）
DBSIZE                        # 当前 DB 键数量
ROLE                          # 查看主从角色
REPLICAOF                     # 配置主从（替代 SLAVEOF）
MEMORY USAGE / STATS / PURGE  # 内存诊断（Redis 4.0+）
MODULE LIST / LOAD / UNLOAD   # 模块管理
MONITOR                       # 实时命令监控（调试用）
SHUTDOWN                      # 安全关闭
TIME                          # 服务器时间戳
LOLWUT                        # 彩蛋命令 😄
```

## 🌐 Cluster（集群｜集群模式专用）
```text
CLUSTER INFO / NODES / SLOTS  # 集群状态
CLUSTER MEET / FORGET         # 节点加入/移除
CLUSTER REPLICATE             # 配置从节点
CLUSTER FAILOVER              # 手动故障转移
CLUSTER KEYSLOT / GETKEYSINSLOT  # 槽计算/查询
CLUSTER ADDSLOTS / DELSLOTS   # 槽分配管理
CLUSTER REPLICAS              # 查看副本节点（替代 SLAVES）
```

## 📏 HyperLogLog
```text
PFADD                         # 添加元素
PFCOUNT                       # 估算基数
PFMERGE                       # 合并多个 HLL
```

## 🌍 Geospatial（地理空间｜基于 Sorted Set）
```text
GEOADD                        # 添加位置（经度,纬度,名称）
GEOPOS / GEOHASH              # 查询坐标/Geohash
GEODIST                       # 计算两点距离
GEOSEARCH / GEOSEARCHSTORE    # 位置查询与存储（Redis 6.2+，替代 GEORADIUS）
```

## 🖼️ Bitmaps（位图｜基于 String 的位操作）
```text
SETBIT / GETBIT               # 设置/获取指定位
BITCOUNT                      # 统计 1 的数量
BITPOS                        # 查找首个 0/1 位置
BITOP AND/OR/XOR/NOT          # 位运算（支持多 key）
BITFIELD                      # 复杂位域操作（GET/SET/INCRBY）
```

---

## 💡 使用建议
1. **避免危险命令**：`KEYS *`、`FLUSHALL` 等需严格管控  
2. **阻塞命令注意**：`BLPOP`/`BRPOP` 等需设置合理超时  
3. **大键操作**：用 `UNLINK` 代替 `DEL`，`SCAN` 代替 `KEYS`  
4. **版本差异**：  
   - Redis 6.0+：ACL、RESP3（`HELLO`）、客户端缓存  
   - Redis 7.0+：Functions、`LMPOP`、`ZMPOP`  
5. **模块扩展**：  
   - JSON：`JSON.SET` / `JSON.GET`（RedisJSON）  
   - Search：`FT.CREATE` / `FT.SEARCH`（RediSearch）  
   - TimeSeries：`TS.ADD` / `TS.RANGE`（RedisTimeSeries）  

---

✅ **推荐行动**  
1. 将本文档保存为 `redis_commands_cheatsheet.md`  
2. 书签收藏：[https://redis.io/commands/](https://redis.io/commands/)  
3. 本地测试：`redis-cli --help` + `COMMAND INFO <cmd>`  

> 🌟 **小技巧**：在 `redis-cli` 中输入命令后加 `?` 可查看用法（如 `SET ?`）  
> 愿这份文档成为你高效使用 Redis 的得力助手！🚀