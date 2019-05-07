
# 
## 优势
- 搜索引擎
- 高可用
    - 上千台服务器分布式部署
    - PB 级可靠性存储
- 可扩展

## TODO

# 基本概念

## 节点
- 候选主节点
    - 可能被选举为主节点的节点
- 主节点
    - `node.master: true`
        - 默认
    - 由候选主节点选举出来
    - 管理 ES 集群
        - 负责索引的 DDL 操作
        - 管理其它节点的分片
    - 通过广播维持连接
- 数据节点
    - `node.data: true`
        - 默认
    - 数据存储
        - CRUD
- 提取节点
    - `node.ingest: true`
    - 索引查询前的文档预处理
        - 时机
            - intercepts bulk
            - index rquest
        - 动作
            - transformations
                - 使用指定了 processors 的 pipeline 进行数据处理
                - 可于 processors 中进行字段的删除/ 重命名等
- 部落节点
    - 用于连接多个集群进行搜索等操作
- 协调节点
    - 每个节点都是，且**不可禁用**
    - 将各分片数据汇总聚集后，返回给客户端

## 索引 
- 基于 `Lucene` 的倒排索引
- 示例
    - 数据 

        | 文档  | 内容         |
        |-------|--------------|
        | Doc-1 | Life goes    |
        | Doc-2 | Life is good |
    - 索引

        | 分词 | 文档        |
        |------|-------------|
        | Life | Doc-1,Doc-2 |
        | goes | Doc-1       |
        | is   | Doc-2       |
        | good | Doc-2       | 

## 分片
- 通过分片来存储索引
    - `Index`= `Shade`* 
- 示例
    - 索引配置有3分片1分片副本
        - `P*` 指代 `primary shard`
        - `R*` 指代 `replica shard`

            | 分片1 | 分片2 | 分片3 |
            |-------|-------|-------|
            | P1    | P2    | P3    |
            | R2    | R3    | R1    |


## 算法
### Quorum
- 默认的副本策略
- 写入超过半数时返回成功

### Gossip 
- 节点发现所用的算法
    - 算法复杂度为 `Lg(N)`
    - 其中 `Pull-Push` 的收敛性最佳
- 示例

    | 方式          | 过程                                                     |
    |---------------|----------------------------------------------------------|
    | A Push B      | A-> DigestA-> B<br>B-> DigestA-B-> A<br>A-> A-B-> B      |
    | A Pull B      | A-> DigestA-> B<br>B-> B-A-> A                           |
    | A Pull-Push B | A-> DigestA-> B<br>B-> B-A& DigestA-B-> A<br>A-> A-B-> B | 

## 流程
### Shade 操作

- 图示
    - ![ES.Request.Flow.jpg](http://doc.yqjdcyy.com/bd426712-cfc2-4cc8-9fad-13293bf5a399.jpg)

- 流程
    - 顺序执行
        - 写入 Transaction Log
        - 写入内存
    - 触发执行
        - 同步至 FileSytem Cache / 1s
        - 将 FileSystem Cache（Index+ Transaction Long) 写入磁盘 / 30s
        - 将 Transaction Log 写入磁盘/ 5s|write|flushed
        - 重置 Transaction Log / 30m|超过上限

# 语句
## 创建
## 查询 
### 条件
### 聚合

# 参数

- `bootstrap.memory_lock: true`
    - 锁定物理内存，避免使用 swag

# 推荐
## kibana

# 常见问题

## 脑裂
- 场景
    - 出现网络分区时，不同节点选中了不同的主节点，导致数据不一致或丢失
- 优化
    - 主节点不担当数据节点
        ```
        node.master: true
        node.data: false
        ```
    - 延长 `discovery.zen.ping_timeout`
        - 默认为 3 秒，延长以增加节点响应的等待时长，减少误判
    - 设置 `discovery.zen.minimum_master_nodes` 为 `（主节点数/2)+ 1`
        - 该参数控制节点需至少连接多少台主节点，才允许进行操作

## 取消 Type
- 避免 type 类比 db.table 的错误理解 
    - ES 不允许同一 Index 下不同 Type 的字段类型不同
- 减少同一索引下，不同实体因字段不同而导致数据稀疏
    - 各 Type 之间要为之间的字段冗余空间，但又非该 Type 所需要的字段，凭空浪费空间
    - 干扰 Lucenne 高效压缩文件
- 评分机制是 Index 维度，不同 Type 之间的评分会造成干扰
- 索引元数据于主节点维护，减少大量变更情况下，对 Index 的堵塞/影响

## Boolean 类型存储 String 数据 
- 描述
    - 测试使用的是 5.6.4 版本
    - 线上使用的是 6.3.0 版本
    - 测试字段上，`text` 字段的类型不小心被设置为 `boolean`，但可正常插入文本信息
    - 在正式环境上，插入时则会提示 `xxxx as only [true] or [false]`
- 处理
    - 需**重建**索引

# 参考
## 官方
- []()
- []()

## 取消单 Index 下多 Type
- [ElasticSearch 6.X 不再支持多个doc_type](https://lunatictwo.github.io/2017/11/18/ElasticSearch%206.X%20%E4%B8%8D%E5%86%8D%E6%94%AF%E6%8C%81%E5%A4%9A%E4%B8%AAdoc_type/)
- [【拓展篇】Elasticsearch 6.0 一个索引只允许有一个type](https://elasticsearch.cn/article/337)

## 其它
- [ElasticSearch 内部机制浅析（一）](https://leonlibraries.github.io/2017/04/15/ElasticSearch%E5%86%85%E9%83%A8%E6%9C%BA%E5%88%B6%E6%B5%85%E6%9E%90%E4%B8%80/)
- []()
- [ElasticSearch 内部机制浅析（三）](https://leonlibraries.github.io/2017/04/27/ElasticSearch%E5%86%85%E9%83%A8%E6%9C%BA%E5%88%B6%E6%B5%85%E6%9E%90%E4%B8%89/)
- [Elasticsearch集群的脑裂问题](https://blog.csdn.net/cnweike/article/details/39083089)
- [Ingest Node（预处理节点）](http://cwiki.apachecn.org/pages/viewpage.action?pageId=9405257)
- [ElasticSearch的节点类型](http://arganzheng.life/elasticsearch-node-types.html)
- [Linux中Swap与Memory内存简单介绍](https://blog.csdn.net/zwan0518/article/details/12059213)
- []()

