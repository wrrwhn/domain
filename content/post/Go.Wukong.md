---
title: "Go.Wukong"
date: "2017-11-15"
categories:
 - "整理"
tags:
 - "Go"
toc: true
---


# 悟空引擎使用

## 参考
- [使用wukong全文搜索引擎](http://tonybai.com/2016/12/06/an-intro-to-wukong-fulltext-search-engine/)
- [huichen/wukong](https://github.com/huichen/wukong)
- [悟空：用Go语言编写的全文搜索引擎](http://www.infoq.com/cn/news/2015/09/wukong-search-engine)
- [huichen/sego](https://github.com/huichen/sego)
- [boltdb/bolt](https://github.com/boltdb/bolt)
- [cznic/kv](https://github.com/cznic/kv)
- [huichen/murmur](https://github.com/huichen/murmur)


## 介绍
### 内部结构
- 主协程，用于收发用户请求
- 分词器（segmenter）协程，负责分词
- 索引器（indexer）协程，负责建立和查找索引表
- 排序器（ranker）协程，负责对文档评分排序

### 流程
#### 索引
- 文档加入索引请求
- 主协程通过 channel 将分要分词的文本发送给分词协程
- 该协程将文本分词后通过另一信道发送给索引器协程
- 索引器协程建立从搜索键到文档的反向索引，并保存在内存中

#### 搜索
- 主协程获取用户搜索请求，将搜索词分词后通过信道传递给索引器
- 索引器查找每个搜索键对应文档，并进行逻辑归并求交集后，得到精简文档列表，并通过信道传递给排序器
- 排序器对文档进行评分、筛选和排序，并通过指定信道返回主协程以返回给用户

## 结构
- core
- data
- docs
- engine
- example
- storate
- testdata
- types
- utils


## 使用
- 基本
    ```
    searcher = engine.Engine{}

    searcher.Init(types.EngineInitOptions{
            IndexerInitOptions: &types.IndexerInitOptions{
                IndexType: types.DocIdsIndex,
            },
            SegmenterDictionaries: "./dict/dictionary.txt",
            StopTokenFile:         "./dict/stop_tokens.txt",
        })
    defer searcher.Close()
    searcher.IndexDocument(docId, types.DocumentIndexData{Content: text1}, false)
    searcher.FlushIndex()
    fmt.Printf("%#v\n", searcher.Search(types.SearchRequest{Text: "巴萨 梅西"}))
    ```

- 序列类型
    - FrequenciesIndex
        - 保留词频
        - `types.SearchResponse{Tokens:[]string{"巴萨", "梅西"}, Docs:[]types.ScoredDocument{types.ScoredDocument{DocId:0x1, Scores:[]float32{3.0480049}, TokenSnippetLocations:[]int(nil), TokenLocations:[][]int(nil)}}, Timeout:false, NumDocs:1}`
    - LocationsIndex
        - 保留关键字在文档中出现的位置信息
        - `types.SearchResponse{Tokens:[]string{"巴萨", "梅西"}, Docs:[]types.ScoredDocument{types.ScoredDocument{DocId:0x1, Scores:[]float32{3.0480049}, TokenSnippetLocations:[]int{37, 76}, TokenLocations:[][]int{[]int{37}, []int{76}}}}, Timeout:false, NumDocs:1}`

- 分词
    - 中文分词基于 `sego` 项目实现
    - 分词准确度影响索引建立和关键词搜索，建议定期更新网络库

- 持久化索引+启动恢复
    - 文档索引存放在内存中
    - 持久化方式
        - boltdb
            - 默认
            - An embedded key/value database for Go.
        - cznic/kv
            - Package kv implements a simple and easy to use persistent key/value (KV) store.
    - 示例
        ```
        searcher.Init(types.EngineInitOptions{
               IndexerInitOptions: &types.IndexerInitOptions{
                   IndexType: types.DocIdsIndex,
               },
               UsePersistentStorage:    true,
               PersistentStorageFolder: "./index",
               SegmenterDictionaries:   "./dict/dictionary.txt",
               StopTokenFile:           "./dict/stop_tokens.txt",
           })
           // ＊
        searcher.FlushIndex()
       ```
- 分布式
    - 按文本内容 hash 值理解试用分至不同服务器索引
    - 请求时分发至所有裂分服务器，后将所有服务器返回结果归并排序后输出
    - 使用 Murmur3 hash 来保证裂分均匀性

## 局限
- 作者个人项目，资料少，开发不活跃
- 缺少开发计划和愿景
- 查询功能简单，仅支持关键词的 AND 操作
- 缺少将索引存储于 DB 的操作支持
