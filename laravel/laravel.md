### 异常
> 路径 `app/Exceptions/Handler.php`
>
> 方法: `report()` 和 `render()`
>
> `report` 方法用于将异常报告给外部服务或者记录日志，默认情况下，将异常传递给记录异常的基类。注意对于添加到 dontReport 属性数组中的异常类，则不会被报告(执行此方法）。
>
> `render` 方法负责将给定的异常转换为将被发送回浏览器的 HTTP 响应。默认情况下，异常将传递给为你生成响应的基类。你可以在这里针对不同的异常，自定义相应的响应。

### 中间件
> 路径 `vendor/laravel/framework/src/Illuminate/Pipeline/Pipeline.php`
>
> 核心方法: `array_reduce()` 和 `array_reverse()`
>
> `array_reverse` 闭包嵌套, 执行顺序是由内向外的, 所以需要array_reverse函数将中间件的顺序反转

### laravel-pipelines
> `https://martinjoo.dev/laravel-pipelines`

### laravel-livewire
> `https://livewire.laravel.com/docs/navigate`

### laravel-workflow
> `https://laravel-workflow.com/docs/defining-workflows/workflow-id/`
>
> `http://wflow.willianfu.top/`

### laravel多租客包
> `https://learnku.com/laravel/t/47951`
>
> `https://tenancyforlaravel.com/`

### Adldap2-Laravel
> `https://adldap2.github.io/Adldap2-Laravel/#/`