
# 数据结构

- SDS
	- len/ free/ char[]
	- SDS_MAX_PREALLOC= 1024* 1024

- List
	- List
		- *ListNode head, tail
		- int len
		- void * dup/ free/ match
	- ListNode
		- *ListNode prev, next
		- void *value

- Dict
	- Dict
		- dickht ht[2]
		- int rehashidx
		- int iterators
	- DictHt
		- dictEntry **table
		- unsigned long size/ sizemask/ used
	- DictEntry
		- void *key
		- void *val
		- DictEntry *next

	- 链接法
	- 开放寻址
	- 干净探测法

	- resize
		- dict_can_resize 
		- 扩容
			- 1
			- 5
			- `>= 2* ht[0].used`
		- 缩容
			- 1/10
			- `>= ht[0].used`


- SkipList
	- ZSkipList
		- ZSkipListNode *head, tail
		- long length
		- int level	< 10
	- ZSkipListNode
		- robj *obj
		- double score
		- ZSkipListNode *back
		- *ZSkipListLevel level
	- ZSkipListLevel
		- ZSkipListLevel *forward
		- unsigned int span

- IntSet
	- set/ order/ long
	- IntSet
		- uint32_t encoding, length
		- int8_t contents[]
	- 扩容
		- 不支持降级
	- 512 限制


- ZipList
	- ZipList
		- zlbytes   4B
		- zltail    4B
		- zllen		2B
		- Entry[]
		- end       1B
	- Entry
		- pre_entry_length	1/5B
		- encoding          2b
		- length            6/14/32b
		- content



# 对象
- RedisObject
	- unsigned type, notuesd, encoding, lru
	- int refcount
	- void *ptr

- REDIS_STRING	
	- REDIS_ENCODING_INT
	- REDIS_ENCODING_EMBSTR
	- REDIS_ENCODING_RAW
- REDIS_LIST	
	- REDIS_ENCODING_LINKEDLIST
	- REDIS_ENCODING_ZIPLIST
- REDIS_SET	
	- REDIS_ENCODING_HT
	- REDIS_ENCODING_INTSET
- REDIS_ZSET	
	- REDIS_ENCODING_SKIPLIST
	- REDIS_ENCODING_ZIPLIST
- REDIS_HASH	
	- REDIS_ENCODING_ZIPLIST
	- REDIS_ENCODING_HT



# Server

- point
	- var
	- load.config
	- daemon
	- init
	- load.db
	- event

- component
	- clients
	- dbs
	- aof|rdb
	- command table
	- event


- Client
	- data
		- net
		- *db
		- *command
	- cache
		- request
		- response
	- stattistic
		- create-time
		- last-time
		- cache

- Event
	- File> Time(delay)
	- File
		- read 	优先
		- write
	- Time
		- when
		- proc
		- next

		- statistic/ rdb/ expire/ copy

# DB
- dict
- expires
	- 定时：过期时间事件
	- 惰性：查询时判断
	- 定期：定期
- watched_keys

# 持久化
- COW
- RDB
	- SAVE
	- BGSAVE
		- 900 1
		- 300	10
	- LOAD
		- 1000 keys

	- File
		- "redis"
		- 0006
		- db.idx
		- key-value* 
			- LZF
		- EOF
		- CHECK

- AOF
	- RESP
	- Cache/ Memory/ AOF.IO
	- MODEL
		- AOF_FSYNC_NO			BLOCK
		- AOF_FSYNC_EVERYSEC		NO_BLOCK
		- AOF_FSYNC_ALWAYS		BLOCK


# 事务
- * multiCmd
	- *redisCommand
	- *8robj	argv
	- int 	argc

- MULTI
- DISCARD
- EXEC
- WATCH

- ACID


# 发布订阅
- subscribe
- psubscribe
- publish


# 集群
- slot= 2^14
- CAP.AP

- redis-trib.rb
- cluster

- 无中心+ PING-PONG

- 一致性哈希	2^32

- upgrade



# copy
- runid+ offset	
- backlog= 1M
