# es

### text �� keyword
> `text`��`ȫ������`��`�ִ�`���ʺϡ�������
>
> `keyword`��`��׼ƥ��`��`���ִ�`���ʺϡ�ɸѡ��

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


### ���̽�� Elasticsearch 8.X��function_score
> `https://zhuanlan.zhihu.com/p/646115404`