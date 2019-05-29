# 搜索
## 精确匹配
- 将搜索内容用**双引号**包起来
    - `"mysql foreign key"`
    - 限制分词操作

## 模糊搜索
- 使用通配符 `*` 进行查询
    - `mysql * key`

## 指定站点搜索
- 搜索内容后接 `site:` 及相应网站
    - `"mysql * key" stackoverflow.com`

## 指定文件类型
- 搜索内容后接 `filetype:`及相应文件类型，多个类型可用 `/` 分隔
    - `elasticsearch filetype:pdf`

## 排查关键字
- 搜索内容后接 `-`及相应的关键字
    - `"mysql foreign key" -外键 -Examples`
    - `-` 与关键字相连，不要有空格

## 多关键词搜索
- 各关键词间使用 ` OR ` 进行分隔
    - `巧克力 OR 白巧克力`

## 数值范围搜索
- 两个数值中间，使用两个英文句号和一个英文空格间隔开
    - `欧冠 连胜 2012.. 2017`

## 特定位置搜索
- `inurl:`/ `intitle:`/ `intext:` / `allinurl|title|text` 后接关键字
    - `inurl:shenzhou.com`
    - `inurl:SEO 搜索引擎`
        - 等价于 `inurl:SEO inurl:搜索引擎`


# 参考
- [如何用好谷歌等搜索引擎？](https://www.zhihu.com/question/20161362)
- [20 Google Search Tips to Use Google More Efficiently](https://www.lifehack.org/articles/technology/20-tips-use-google-search-efficiently.html)

