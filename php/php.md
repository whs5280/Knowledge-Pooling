# php

### Դ�������
> �����ĵ� `https://wiki.php.net/internals/windows/stepbystepbuild`
> + `https://windows.php.net/downloads/php-sdk/archives/`
> + `https://windows.php.net/downloads/php-sdk/deps/`
> 
> �̳� `https://learnku.com/articles/22323`
>
> ���� `https://github.com/Microsoft/php-sdk-binary-tools/tree/legacy`
>
> ��ϸ���� `https://blog.csdn.net/weixin_29825411/article/details/115236412`


### Grpc��չ��
> `https://www.cnblogs.com/tinywan/p/13863805.html`
>
> `https://pecl.php.net/package/gRPC`


### CGI��php-cgi��FastCGI��php-fpm
> `cgi` ����Э�飬������ Web ���������� Apache, Nginx�����ⲿӦ�ó����� PHP, Python ����֮����ν���ͨ�ŵĹ���
>
> `php-cgi` PHP ��������ÿ����������һ���µĽ���������Ƶ�����������٣����ܲ�
>
> `fastcgi` ��ǿ��Э�顣������� `CGIģʽ` Ƶ���������ٽ��̵�����
>
> `php-fpm` ʵ���� `FastCGI`Э�飬��Ϊһ�������̣���������������ͻ��ն�� `php-cgi` �ӽ���


### FastCGI �� php-fpm��ϵ
> `php-fpm master �� worker ���� �� ���� FastCGI ����`


| ���� | ��� | ���� | �����ص� |
| :--- | :--- | :--- | :--- |
| `CGI`     | **Э��/��׼**   | Web ���������ⲿӦ�ó���ͨ�ŵĹ��Ϲ淶��                      | һ������һ�����̡����ܲ |
| `php-cgi` | **����/������** | PHP �� `CGI Э��` �ľ���ʵ�֡�                           | ʵ���� CGI Э�飬������ģʽ�� CGI ��ͬ��ÿ������ fork �½��̣��� |
| `FastCGI` | **Э��/��׼**   | `CGI ����ǿ��Э��`�������˳�פ���̵�ͨ�ŷ�ʽ��               | ���̳�פ��ͨ�� Socket ͨ�š����ܸߣ����ִ�������׼�� |
| `php-fpm` | **����/������** | PHP �� `FastCGI Э��` ��**���ʵ��**���������˽��̹����ܡ� | ����һ�� PHP ���̳أ��ṩƽ����������̬���ŵȹ��ܣ����ִ��������� Nginx+PHP���ľ��������� |


### php-fpm����ģʽ
> `static` ��̬������ʱ�����̶��Ľ�������`pm.max_children`��������
>
> `dynamic` �ӽ����������ݸ����ڷ�Χ�ڶ�̬������������������
> + `pm.max_children` ���̳�����
> + `pm.start_servers` ����ʱ������
> + `pm.min_spare_servers` ����״̬�µ���С���н���������֤��Ͳ���������
> + `pm.max_spare_servers` ����״̬�µ������н��������������ʱռ�ù����ڴ棩
>
> `ondemand` ���贴�����̡����н����ڳ�ʱ��ᱻ�ͷ�

### �������ջ��ƣ�gc��
> `php5.3֮ǰ`
> + `���ü���`��`zval`�ı�������������������һ�Σ�������һ������������Ϊ0������������ [ѭ�������޷����������ڴ�й©]
>   + `zval`�ṹ��`{refcount��is_ref}`

> `php5.3-5.6`
> + `���ü���`+`ͬ�����ڻ����㷨` [�ܹ����ѭ�����ò�����]
>   + ����������ʱ��Ĭ��10,000�����ܸ���
>   + ���� `gc_collect_cycles()` ����ʱ
> + `ͬ�����ڻ����㷨`
>  1. �����������������ܸ���: `refcount ���ٵ����� 0 ʱ` �����������
>  2. ģ��ɾ��������`������ȱ����㷨`��������������Ա `refcount - 1`
>  3. ģ��ָ���������ʵ�����������һ�������� `refcount` �ڲ���2 ����� 0��˵�������������ָ�`refcount + 1`
>  4. ����ɾ���������ڴ棩��`refcount=0`����ɾ��

> `php7.x`
> + `���ü���`+`ͬ�����ڻ����㷨` (����php5.3)
> + `zval` �ṹ�Ż� `https://learnku.com/articles/33451`
>  + ����������`����` `������` `������`���ٵ����洢���ü�������ֵʱֱ�ӽ��п���
>  + ������������`����` `����` `�ַ���`�����ü���������洢�����ü���`refcount`ֱ�Ӵ洢�����������Ӧ�Ľṹ`zend_array` `zend_object`��������ͨ�� `zval` ��Ӵ洢


| �汾���� | PHP 5.2 ��֮ǰ | PHP 5.3 - 5.6 | PHP 7.x | PHP 8.x |
|---------|--------------|--------------|---------|---------|
| **���Ļ���** | ���������ü��� | ���ü��� + ͬ�����ڻ����㷨 | �Ż������ü��� + ͬ�����ڻ����㷨 | ��һ���Ż������ü��� + ͬ�����ڻ����㷨 |
| **zval�ṹ** | ÿ��zval������refcount��is_ref | ͬ�� | �����������洢����refcount����������refcount�洢������ṹ | ����PHP7�Ż���������������洢�Ż� |
| **ѭ�����ô���** | ? �޷��������ڴ�й© | ? �ܹ����ͻ��� | ? ����Ч�ؼ��ͻ��� | ? ���Ż��ļ��ͻ��� |
| **�ڴ�ʹ��** | �ϸߣ�ÿ��zval����Ҫ�洢������Ϣ | ͬ�� | �������ͣ������������ü������� | ��һ���Ż��ڴ�ռ�� |
| **���ܱ���** | �򵥵�����������й© | ����GC���������й©���� | ���ܴ��������GC������С | ������ܣ�GCЧ����� |
| **��������** | ���ü���Ϊ0ʱ�����ͷ� | 1. ����������(Ĭ��10,000)<br>2. �ֶ�����gc_collect_cycles() | ͬPHP5.3���ƣ���ִ��Ч�ʸ��� | ͬPHP7���㷨��һ���Ż� |
| **����ѡ��** | ��ר������ | zend.enable_gc, gc_enable()/disable() | ͬPHP5.3������GCͳ�ƺ��� | ����֮ǰ���ã��ȶ��Ը�ǿ |
| **��Ҫ�Ľ�** | �������ü��� | ���ѭ���������� | zval�ṹ�Ż����������� | �ȶ��Ժͱ�Ե����Ż� |


### �ڴ�������洦��
> `ini_set(��memory_limit��,����)`
>
> `object` -> `array`
>
> `array_chunk()`


### �����������հ���
> 1. ��Ϊ�ص�����
> 2. �¼�������̣����������/�м��
> 3. �ӳ�ִ�� / ��װ�߼�

> ������ `Closure`����
> + `$greet instanceof Closure`


### namespace �� composer
> `composer` �Զ���������
>
> `user namespace` ��������

> ִ�й���
> 1. ���� `new User()`������ǰ�����ռ�û�� User ��
> 2. ���� `User` ������������ `User` ��Ӧ `App\Models\User`
> 3. ��`composer` �� `classmap` �ҵ� `App\Models\User`
> 4. �ҵ���Ӧ�ļ�·��������


### �Զ����ػ����Լ�laravel��ʵ��
> 1. ����һ��û�е��࣬����õ�ħ������`__autoload()`
> 2. `__autoload()` ͨ��`classmap`ӳ�� �ҵ���Ӧ�ļ�·����`include`��`require`��ȱ����ȫ��ֻ�ܶ���һ�����޷��������Զ����ز��� [7.2����]
> 3. �Ľ�֮��`spl_autoload_register()`����� ��� `Composer` �� `PSR-4`
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

### �����ݡ��߲����²�ѯ�������ݣ�ʵ�����ݴ��ڣ�
> ���Ӳ�ͬ��
>
> ���ݿ���뼶��


### swoole�ĵ�
> `https://wiki.swoole.com/zh-cn/#/environment`


### hyperf�ĵ�
> `https://hyperf.wiki/3.0/#/`


### �����ʼ�
> `https://www.kancloud.cn/luke8327/phpwolf/2556957`


### ����Ա�ܽ�
> `https://www.kancloud.cn/nickpang/phper/2400752`


### php-xlsWriter
> `https://xlswriter-docs.viest.me`


### php�ֲ�
> `https://www.php.cn/manual/view/256.html`


### Xdebug+phpStorm
> `https://developer.aliyun.com/article/1336678`


### PHPSeniorInterview
> `https://github.com/CoderOpen/PHPSeniorInterview`


### ��������淶
> `https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-1-basic-coding-standard-cn.md`