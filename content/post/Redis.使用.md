---
title: "Redis.使用"
date: "2017-11-15"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---


## 简介
### 介绍
- 支持网络，可基于内存亦可持久化的日志型、Key-Value数据库


## 安装-WINDOWS
- 下载                    `http://www.Juming.com/Csdn/dx/?i=712654422676017&s=a2b84b5fc8f9fa297831deba85c07317`
- 解压至 D:\redis-2.0.2，并更新替换redis.conf
- cmd执行 `D:\redis-2.0.2>redis-server.exe redis.conf` 运行服务器
- cmd执行 `D:\redis-2.0.2>redis-cli.exe -h 192.168.10.59 -p 6379` 运行客户端
- 客户端执行 set name yao设置name=yao，后执行get name获取redis中name的值


## 安装-LINUX
### VI环境下输入
- 下载程序
    + `wget http://download.redis.io/releases/redis-2.8.3.tar.gz`
- 解压
    + `tar xzf redis-2.8.3.tar.gz`
- 跳转至解压目录
    + `cd redis-2.8.3`
- 安装Redis
    + `make PREFIX=/usr/local/redis install`
        * 若提示make指定未安装，执行`yum -y install gcc automake autoconf libtool make` 进行安装
- 拷贝执行相关
    + src路径下的redis-server、redis-benchmark、redis-cli，redis-2.8.3目录下redis.conf至服务目录/usr/local/redis
    - `mkdir -p /usr/local/redis`
    - `cp redis-server redis-benchmark redis-cli /usr/local/redis`
    - `cd /usr/local/redis`
- 启动服务                 
    + `./redis-server   redis.conf`
        - 若服务未后台启动，修改redis.conf的daemonize no为yes
        - 对外情况下，需将 bind 配置一率关闭，同时将 protected-mode 更新为 no
- 启动客户端
    + `./redis-cli`
- 写数据
    + `set name yao`
- 读数据
    + `get name`



## 调用-Jedis
### 实例

``` java
import redis.clients.jedis.Jedis;
public class Client {
    public static void main(String[] args) {
        Jedis jedis= new Jedis("localhost");
        jedis.set("age", "25");
        String name= jedis.get("name");
        String age= jedis.get("age");
        System.out.println("[name: "+ name+", age: "+ age+"]");
    }
}
```

### 常用方法
- 新增覆盖               
    + `jedis.set("name", "minxr");`
- 获取                   
    + `jedis.get("name");`
- 追加                   
    + `jedis.append("name", "jintao");`
- 删除                   
    + `jedis.del("name");`
- 清空数据               
    + `jedis.flushDB();`
- 校验存在               
    + `jedis.exists("foo");`
- 不存在时添加否则不操作 
    + `jedis.setnx("foo", "foo not exits");`
- 2秒有效                 
    + `jedis.setex("foo", 2, "foo not exits");`
- 截取值                 
    + `jedis.getrange("foo", 1, 3);`
- 批量赋值               
    + `jedis.mset("name", "minxr", "jarorwar", "aaa");`
- 批量获取               
    + `jedis.mget("mset1", "mset2", "mset3", "mset4");`
- 批量删除               
    + `jedis.del(new String[] { "foo", "foo1", "foo3" });`

## 实例
### 调用实现

``` java
org.springframework.data.redis.core.StringRedisTemplate
@Autowired
StringRedisTemplate stringRedisTemplate;
```

### 数据结构

``` sh
blog:id:hash
  id  1
  likeCount 12
  blog  [...]
blog:zset
  1 10
  2 2
  4 0
```

### 工具类

``` java
public class RedisKeyUtils {
    public static String generate(String key, Long id) {    //blog:l:%d:hash  10 -> blog:l:10:hash
        return String.format(key, id);
    }
}

public class RedisSettings {    //Redis相关配置参数对象Bean
    private String blogHashKey;
    private String blogSortedSetKey;
}

public class ColleagueCircleConfig {      //Redis初始化
    @Autowired
    Environment environment;
    @Bean
    public RedisSettings microBlogRedisSettings() {
        RedisSettings microBlogRedisSettings = new RedisSettings();
        microBlogRedisSettings.setBlogSortedSetKey("blog:zset");
        microBlogRedisSettings.setBlogHashKey("blog:%d:hash");
        microBlogRedisSettings.setBlogCommentSortedSetKey("blog:%d:c:zset");
        microBlogRedisSettings.setBlogCommentHashSetKey("blog:c:%d:hash");
    }
}
@Configuration
@Import(value = {ColleagueCircleConfig.class})
public class AppConfig extends AbstractAppConfig    //于系统启动时进行初始化
```

### 业务操作
#### 新增

``` java
String likeHashKey = RedisKeyUtils.generate(redisSettings.getBlogLikeHashSetKey(), microBlogLike.getBlogId());
addToLikeHash(likeHashKey, microBlogLike);
stringRedisTemplate.opsForHash().put(likeHashKey, microBlogLike.getId().toString(), jsonConverter.toJson(microBlogLike));

stringRedisTemplate.opsForHash().increment(blogKey, MicroBlogServiceRedisImpl.BLOG_HASH_FIELD_LIKE_COUNT, 1);


BoundHashOperations boundHashOperations = stringRedisTemplate.boundHashOps(key);
Map<String, Object> map = new HashMap<>();
map.put(BLOG_HASH_FIELD_BLOG, jsonConverter.toJson(microBlog));
map.put(BLOG_HASH_FIELD_ATTACHMENTS, jsonConverter.toJson(attachments));
map.put(BLOG_HASH_FIELD_RECEIVERS, jsonConverter.toJson(receivers));
map.put(BLOG_HASH_FIELD_COMMENT_COUNT, "0");
map.put(BLOG_HASH_FIELD_LIKE_COUNT, "0");
boundHashOperations.putAll(map);
```

#### 删除

``` java
String key = RedisKeyUtils.generate(redisSettings.getBlogLikeHashSetKey(), blogId);
stringRedisTemplate.opsForHash().delete(key, microBlogLikeId.toString());
```

#### 查询   

``` java
String hashKey = RedisKeyUtils.generate(redisSettings.getBlogLikeHashSetKey(), blogId);
HashOperations<String, String, String> hashOperations = stringRedisTemplate.opsForHash();
List<String> jsonList = hashOperations.multiGet(hashKey, microBlogLikeIdSet);

stringRedisTemplate.opsForZSet().reverseRange(sortedSetKey, 0, pageCount - 1);
```

## 调用-SpringDataRedis
### Key           
- `redisTemplate.hasKey(key)`
- `redisTemplate.delete(key);`
### String   
### List   
### Set   
### Sorted Set   
- `redisTemplate.boundZSetOps(key).add(baseQaAnswer.getId(), 0);`
- `redisTemplate.boundZSetOps(QaRedisConfig.QUESTION_ZSET_KEY_DAY).incrementScore(baseQaAnswer.getQuestionId(), hotCnt);`
- `redisTemplate.boundZSetOps(QaRedisConfig.QUESTION_ZSET_KEY).remove(id);`
- `redisTemplate.opsForZSet().reverseRange(key, startIndex, endIndex);`
### Hash   
- `BoundHashOperations boundHashOperations = redisTemplate.boundHashOps(key);`
    + `boundHashOperations.putAll(map);`
    + `boundHashOperations.expire(QaRedisConfig.QA_EXPIRE_HOURS, TimeUnit.HOURS);`
    + `boundHashOperations.increment(userId.toString(), 1);`
- `redisTemplate.opsForHash().multiGet(key, HASH_FIELDS_ANSWER);`




### 参考
- [官网](http://redis.io)
- [文档](http://redis.io/documentation)
- [命令](http://redis.readthedocs.org/en/2.4/index.html)
- [Linux下安装](http://www.php100.com/html/webkaifa/PHP/PHPyingyong/2011/0406/7873.html)
- [Jedis使用示例](http://javacrazyer.iteye.com/blog/1840161)
- [配置redis外网可访问](https://blog.csdn.net/hel12he/article/details/46911159)
- [Linux下redis安装和部署](https://www.jianshu.com/p/bc84b2b71c1c)