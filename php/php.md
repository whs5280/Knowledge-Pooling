# php

### 源代码编译
> 官网文档 `https://wiki.php.net/internals/windows/stepbystepbuild`
> + `https://windows.php.net/downloads/php-sdk/archives/`
> + `https://windows.php.net/downloads/php-sdk/deps/`
> 
> 教程 `https://learnku.com/articles/22323`
>
> 工具 `https://github.com/Microsoft/php-sdk-binary-tools/tree/legacy`
>
> 详细步骤 `https://blog.csdn.net/weixin_29825411/article/details/115236412`


### Grpc扩展包
> `https://www.cnblogs.com/tinywan/p/13863805.html`
>
> `https://pecl.php.net/package/gRPC`


### CGI、php-cgi、FastCGI、php-fpm
> `cgi` 请求协议，定义了 Web 服务器（如 Apache, Nginx）和外部应用程序（如 PHP, Python 程序）之间如何进行通信的规则
>
> `php-cgi` PHP 解析器，每次请求都启动一个新的进程来处理，频繁创建和销毁，性能差
>
> `fastcgi` 增强版协议。它解决了 `CGI模式` 频繁创建销毁进程的问题
>
> `php-fpm` 实现了 `FastCGI`协议，作为一个主进程，负责启动、管理和回收多个 `php-cgi` 子进程


### FastCGI 和 php-fpm关系
> `php-fpm master → worker 进程 → 监听 FastCGI 请求`


| 名称 | 类别 | 描述 | 核心特点 |
| :--- | :--- | :--- | :--- |
| `CGI`     | **协议/标准**   | Web 服务器与外部应用程序通信的古老规范。                      | 一次请求，一个进程。性能差。 |
| `php-cgi` | **程序/解释器** | PHP 对 `CGI 协议` 的具体实现。                           | 实现了 CGI 协议，但性能模式与 CGI 相同（每次请求 fork 新进程）。 |
| `FastCGI` | **协议/标准**   | `CGI 的增强版协议`，定义了常驻进程的通信方式。               | 进程常驻，通过 Socket 通信。性能高，是现代主流标准。 |
| `php-fpm` | **程序/管理器** | PHP 对 `FastCGI 协议` 的**最佳实现**，并包含了进程管理功能。 | 管理一个 PHP 进程池，提供平滑重启、动态调优等功能，是现代环境（如 Nginx+PHP）的绝对主力。 |


### php-fpm运行模式
> `static` 静态，启动时创建固定的进程数、`pm.max_children`参数决定
>
> `dynamic` 子进程数量根据负载在范围内动态调整，适用生产环境
> + `pm.max_children` 进程池上限
> + `pm.start_servers` 启动时创建数
> + `pm.min_spare_servers` 空闲状态下的最小空闲进程数（保证最低并发能力）
> + `pm.max_spare_servers` 空闲状态下的最大空闲进程数（避免空闲时占用过多内存）
>
> `ondemand` 按需创建进程。空闲进程在超时后会被释放

### 垃圾回收机制（gc）
> `php5.3之前`
> + `引用计数`，`zval`的变量容器，变量被引用一次，计数加一，变量引用数为0，变量被销毁 [循环引用无法处理，导致内存泄漏]
>   + `zval`结构：`{refcount、is_ref}`

> `php5.3-5.6`
> + `引用计数`+`同步周期回收算法` [能够检测循环引用并回收]
>   + 根缓冲区满时（默认10,000个可能根）
>   + 调用 `gc_collect_cycles()` 函数时
> + `同步周期回收算法`
>  1. 发现疑似垃圾（可能根）: `refcount 减少但大于 0 时` 放入根缓冲区
>  2. 模拟删除：采用`深度优先遍历算法`，将根缓冲区成员 `refcount - 1`
>  3. 模拟恢复（鉴别真实垃圾）：如果一个变量的 `refcount` 在步骤2 后大于 0，说明不是垃圾，恢复`refcount + 1`
>  4. 物理删除（回收内存）：`refcount=0`变量删除

> `php7.x`
> + `引用计数`+`同步周期回收算法` (沿用php5.3)
> + `zval` 结构优化 `https://learnku.com/articles/33451`
>  + 简单数据类型`整型` `浮点型` `布尔型`不再单独存储引用计数，赋值时直接进行拷贝
>  + 复杂数据类型`数组` `对象` `字符串`的引用计数由自身存储，引用计数`refcount`直接存储在它们自身对应的结构`zend_array` `zend_object`，而不是通过 `zval` 间接存储


| 版本特性 | PHP 5.2 及之前 | PHP 5.3 - 5.6 | PHP 7.x | PHP 8.x |
|---------|--------------|--------------|---------|---------|
| **核心机制** | 单纯的引用计数 | 引用计数 + 同步周期回收算法 | 优化的引用计数 + 同步周期回收算法 | 进一步优化的引用计数 + 同步周期回收算法 |
| **zval结构** | 每个zval都包含refcount和is_ref | 同左 | 简单类型内联存储，无refcount；复杂类型refcount存储在自身结构 | 延续PHP7优化，引入更多内联存储优化 |
| **循环引用处理** | ? 无法处理，会内存泄漏 | ? 能够检测和回收 | ? 更高效地检测和回收 | ? 最优化的检测和回收 |
| **内存使用** | 较高，每个zval都需要存储引用信息 | 同左 | 显著降低，简单类型无引用计数开销 | 进一步优化内存占用 |
| **性能表现** | 简单但长期运行易泄漏 | 引入GC开销但解决泄漏问题 | 性能大幅提升，GC开销更小 | 最佳性能，GC效率最高 |
| **触发条件** | 引用计数为0时立即释放 | 1. 根缓冲区满(默认10,000)<br>2. 手动调用gc_collect_cycles() | 同PHP5.3机制，但执行效率更高 | 同PHP7，算法进一步优化 |
| **配置选项** | 无专门配置 | zend.enable_gc, gc_enable()/disable() | 同PHP5.3，增加GC统计函数 | 延续之前配置，稳定性更强 |
| **主要改进** | 基础引用计数 | 解决循环引用问题 | zval结构优化，性能提升 | 稳定性和边缘情况优化 |


### 内存溢出常规处理
> `ini_set(‘memory_limit’,’’)`
>
> `object` -> `array`
>
> `array_chunk()`


### 匿名函数（闭包）
> 1. 作为回调函数
> 2. 事件驱动编程，定义监听器/中间件
> 3. 延迟执行 / 封装逻辑

> 本质是 `Closure`对象
> + `$greet instanceof Closure`


### namespace 和 composer
> `composer` 自动依赖引入
>
> `user namespace` 声明该类

> 执行过程
> 1. 看到 `new User()`，但当前命名空间没有 User 类
> 2. 查找 `User` 的声明，发现 `User` 对应 `App\Models\User`
> 3. 在`composer` 的 `classmap` 找到 `App\Models\User`
> 4. 找到对应文件路径并加载


### 自动加载机制以及laravel的实现
> 1. 加载一个没有的类，会调用到魔术函数`__autoload()`
> 2. `__autoload()` 通过`classmap`映射 找到对应文件路径并`include`或`require`，缺点是全局只能定义一个，无法管理多个自动加载策略 [7.2废弃]
> 3. 改进之后`spl_autoload_register()`，结合 结合 `Composer` 的 `PSR-4`
>  + `classmap`
>  + `files`
>  + `namespaces`
>  + `psr4`

```shell script
{
    "autoload": {
        "psr-4": {
            "App\\": "src/",
            "Tests\\": "tests/"
        },
        "classmap": [
            "database/seeds/",
            "database/factories/"
        ],
        "files": [
            "src/helpers.php"
        ]
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    }
}
```

### 大数据、高并发下查询不到数据（实际数据存在）
> 主从不同步
>
> 数据库隔离级别


### swoole文档
> `https://wiki.swoole.com/zh-cn/#/environment`


### hyperf文档
> `https://hyperf.wiki/3.0/#/`


### 开发笔记
> `https://www.kancloud.cn/luke8327/phpwolf/2556957`


### 程序员总结
> `https://www.kancloud.cn/nickpang/phper/2400752`


### php-xlsWriter
> `https://xlswriter-docs.viest.me`


### php手册
> `https://www.php.cn/manual/view/256.html`


### Xdebug+phpStorm
> `https://developer.aliyun.com/article/1336678`


### PHPSeniorInterview
> `https://github.com/CoderOpen/PHPSeniorInterview`


### 基本代码规范
> `https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-1-basic-coding-standard-cn.md`