#tcp

### tcp的发展
> `HTTP/1.0` 一个`TCP`连接只能串行地处理一个请求
>
> `HTTP/1.1` 一个`TCP`连接只能串行地处理多个请求，引入了`持久连接`，又称`连接复用`
>
> `HTTP/2.0` 一个`TCP`并发处理多个请求，多路复用

### 同一个请求在同一个浏览器
> 在同一个浏览器（非无痕窗口）中，浏览器会对相同 URL 的请求进行排队处理
>
> 同域名请求被浏览器序列化  =>  "变成串行请求，非并发"

### header->Content-Type
> `media-type: application/x-www-form-urlencoded` 请求参数在`Form Data`中，键值对，用间隔分开，如： name1=value1&name2=value2
>
> `multipart/form-data` 请求参数在`Request Payload` 中，最后会转化为一条由boundary字符串分隔的信息体，适合二进制文件传输
>
> `application/json`  请求参数在`Request Payload`中， 参数形式：{key:value}

### 重试机制
> `指数退避调度` 每次失败后，延迟时间呈指数增长，防止 “雪崩效应”，加入`抖动机制` 随机抖动