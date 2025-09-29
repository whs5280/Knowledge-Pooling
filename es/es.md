# es

### text 和 keyword
> `text`：`全文搜索`、`分词`，适合“搜索”
>
> `keyword`：`精准匹配`、`不分词`，适合“筛选”

```shell script
designer_section: {
    type: "keyword",
    fields: {
      fh: {
          type: "text",
          analyzer: "fenghao_split"
      }
    }
}
```


### 深度探索 Elasticsearch 8.X：function_score
> `https://zhuanlan.zhihu.com/p/646115404`