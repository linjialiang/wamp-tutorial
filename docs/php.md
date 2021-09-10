# php 操作篇

WAMP 下对 PHP 的配置相对简单，下面我们就开始吧

## 关于 php 配置文件

php 根目录下有 2 个配置文件模板，我们复制其中 1 个并重命名为 php.ini 即可！

-   开发环境示例模板： php.ini-development
-   部署环境示例模板： php.ini-production

WAMP 通常是开发环境，我们这里参考的是 php.ini-development

## 绑定到 httpd

将 php 解释器做为模块绑定到 httpd，在 [httpd 操作篇](./httpd.md) 中已经详细讲解过

## 关于 php 模块

Win 下的 php 通常都是使用官方构建的二进制包，所以除了内置扩展外，其它扩展需要通过 php.ini 文件加载

### php 模块路径

Win 默认情况下认为 php 在 C 盘根目录下，所以默认存放模块目录别识别为 c:\php\etc

显然这是不对的，我们需要自己在 php.ini 文件中设置正确的模块路径

```ini
# 732 行，添加
extension_dir = "c:\wamp\base\php\etc"
```

### php 载入模块

php 采用相对于 extension_dir 设置的路径载入模块，所以必须在 extension_dir 之后才能生效，部分模块需要依赖项支持

| 格式   | 指令                         |
| ------ | ---------------------------- |
| 格式 1 | extension=php_moduleNmae.dll |
| 格式 2 | extension=moduleNmae         |

### php 常用模块

-   php_curl.dll

    ```text
    - 类型： 二进制包自带模块
    - 作用： 让 php 能够连接通讯各种服务器、使用各种协议
    - 依赖： 依赖 php_openssl.dll 模块
    ```

-   php_gd2.dll

    ```text
    - 类型： 二进制包自带模块
    - 作用： 图像处理
    - 依赖： 无
    ```

-   php_mbstring.dll

    ```text
    - 类型： 二进制包自带模块
    - 作用： 支持多字节
    - 依赖： 无
    ```

-   php_mysqli.dll

    ```text
    - 类型： 二进制包自带模块
    - 作用： 对 mysql 的 mysqli 语法支持
    - 依赖： 无
    ```

-   php_pdo_mysql.dll

    ```text
    - 类型： 二进制包自带模块
    - 作用： 对 mysql 支持 pdo 语法支持
    - 依赖： 无
    ```

-   php_openssl.dll

    ```text
    - 类型： 二进制包自带模块
    - 作用： 对 openssl 支持
    - 依赖： 无
    ```

-   php_imagick.dll

    ```text
    - 类型： http://pecl.php.net/ 下载模块
    - 作用： 比gd2更好的处理图形操作
    - 依赖： https://windows.php.net/downloads/pecl/releases/ 下载imagick运行库
    ```

-   php_redis.dll

    ```text
    - 类型： http://pecl.php.net/ 下载模块
    - 作用： 比gd2更好的处理图形操作
    - 依赖： https://windows.php.net/downloads/pecl/releases/ 下载redis运行库
    ```

### php 错误提示

php.ini 通过对 display_errors 的设置控制是否对 php 语法错误进行提示，默认开启错误提示

| 指令                 | 错误提示     |
| -------------------- | ------------ |
| `display_errors=On`  | 开启错误提示 |
| `display_errors=Off` | 关闭错误提示 |

### php 错误提示级别

php 通过 error_reporting 的设置控制错误提示级别，格式如下：

```ini
; php.ini配置文件内格式
error_reporting=错误级别别名

; .php扩展文件内格式
error_reporting(错误级别别名与位运算符组合);
```

### 错误级别

[错误级别一览表](http://php.net/manual/zh/errorfunc.constants.php)

| error-id | error-name          | 描述                                                          |
| -------- | ------------------- | ------------------------------------------------------------- |
| 1        | E_ERROR             | 致命的运行时错误                                              |
| 2        | E_WARNING           | 运行时警告                                                    |
| 4        | E_PARSE             | 编译时语法解析错误                                            |
| 8        | E_NOTICE            | 运行时通知                                                    |
| 16       | E_CORE_ERROR        | 在 PHP 初始化启动过程中发生的致命错误                         |
| 32       | E_CORE_WARNING      | PHP 初始化启动过程中发生的警告                                |
| 64       | E_COMPILE_ERROR     | 致命编译时错误。                                              |
| 128      | E_COMPILE_WARNING   | 编译时警告                                                    |
| 256      | E_USER_ERROR        | 用户产生的错误信息。                                          |
| 512      | E_USER_WARNING      | 用户产生的警告信息。                                          |
| 1024     | E_USER_NOTICE       | 用户产生的通知信息。                                          |
| 2048     | E_STRICT            | 启用 PHP 对代码的修改建议。                                   |
| 4096     | E_RECOVERABLE_ERROR | 可被捕捉的致命错误。                                          |
| 8192     | E_DEPRECATED        | 运行时通知                                                    |
| 16384    | E_USER_DEPRECATED   | 用户产少的警告信息。                                          |
| 30719    | E_ALL               | 支持的所有错误和警告，但 PHP 5.4.0 之前的级别 E_STRICT 除外。 |

可以使用 [php 位运算符规则](http://php.net/manual/zh/language.operators.bitwise.php) 来组合这些值或者屏蔽某些类型的错误

| command    | alias_name          | 结果                                                     |
| ---------- | ------------------- | -------------------------------------------------------- |
| `$a & $b`  | And（按位与）       | 将把 $a 和 $b 中都为 1 的位设为 1。                      |
| `$a 1 $b`  | Or（按位或）        | 将把 $a 和 $b 中任何一个为 1 的位设为 1。                |
| `$a ^ $b`  | Xor（按位异或）     | 将把 $a 和 $b 中一个为 1 另一个为 0 的位设为 1。         |
| `~ $a`     | Not（按位取反）     | 将 `$a` 中为 0 的位设为 1，反之亦然。                    |
| `$a << $b` | Shift left（左移）  | 将 $a 中的位向左移动 $b 次（每一次移动都表示"乘以 2"）。 |
| `$a >> $b` | Shift right（右移） | 将 $a 中的位向右移动 $b 次（每一次移动都表示"除以 2"）。 |

> 提示：在 php.ini 配置文件里，只支持 `|` `~` `!` `^` `&` 这 5 个位运算符

1.  php.ini 配置文件版

    说明：错误达到指定的错误级别才会提示错误报告，下面是案例：

    ```ini
    # 报告所有php错误
    error_reporting = E_ALL

    # 报告所有php错误，但是忽略：1）运行时通知；2）PHP对代码的修改建议
    E_ALL & ~E_DEPRECATED & ~E_STRICT
    ```

    > 注意：只能一个设置生效，所以 php.ini 文件里不要出现多个

2.  `.php`扩展文件内格式

    运用场景：一般是某个.php 扩展文件执行时遇到问题，针对当前文件开启调试功能，下面是案例：

    ```php
    // 关闭错误报告
    error_reporting(0);

    // 报告 runtime 错误
    error_reporting(E_ERROR | E_WARNING | E_PARSE);

    // 报告所有错误
    error_reporting(E_ALL);

    // 等同 error_reporting(E_ALL);
    ini_set("error_reporting", E_ALL);

    // 报告 E_NOTICE 之外的所有错误
    error_reporting(E_ALL & ~E_NOTICE);
    ```

    > 允许覆盖 php.ini 对应配置，只能一个设置生效，并且需要放在最靠近 `<?php` 的位置！

## `php.ini` 文件其它配置附录

> 文件上传相关配置列表：

| command                     | 描述                                                             |
| --------------------------- | ---------------------------------------------------------------- |
| `file_uploads = On`         | 允许 HTTP 文件上传                                               |
| `file_uploads = Off`        | 禁止 HTTP 文件上传                                               |
| `upload_tmp_dir = 指令目录` | 用于 HTTP 上载文件的临时目录（如果没有指定的话，将使用系统默认） |
| `upload_max_filesize = 2M`  | 上传文件的最大允许大小为 2M                                      |
| `max_file_uploads = 20`     | 可以通过单个请求上传的最大文件数量                               |
| `post_max_size = 8M`        | PHP 将接受的 POST 数据的最大大小                                 |

> php 执行脚本相关

| command                   | 描述                                                        |
| ------------------------- | ----------------------------------------------------------- |
| `max_execution_time = 30` | 每个脚本的最大执行时间，以秒为单位（30 秒）                 |
| `max_input_time = 60`     | 每个脚本可能花费解析请求数据的最长时间，以秒为单位（60 秒） |
| `memory_limit = 128M`     | 脚本可能消耗的最大内存数量（128 MB）                        |

## 删除 `php.ini` 多余的配置

> 主要就是删除 php.ini 文件下的备注行，详情见 [php.ini 文件](./source/php.ini)

## 备注

1.  php 所有扩展都已经备注，需自行去 php.ini 开启；
2.  php_xdebug 写在 php.ini 最底部，默认开启
3.  php_xdebug 扩展默认配置支持 phpstrom，如果使用其它 ide 开发项目，请自行配置 ide

## 安装 `php_imagick.dll` 扩展

`php_imagick.dll` 扩展安装需要下载两个软件包：

| 软件包                                                      | 描述                     |
| ----------------------------------------------------------- | ------------------------ |
| [php_imagick](https://pecl.php.net/package/imagick)         | 实现 imagick 功能的扩展  |
| [ImageMagick](https://windows.php.net/downloads/pecl/deps/) | php_imagick 扩展的运行库 |

> 提示：`ImageMagick` 官网下载的安装包可能和 php_imagick 扩展版本冲突，建议通过表中链接下载对应版本！

具体操作步骤如下：

| 步骤 | 描述                                                     |
| ---- | -------------------------------------------------------- |
| 01   | 将 `php_imagick.dll` 拷贝到 php 的扩展目录               |
| 02   | php.ini 文件增加 `extension=imagick`                     |
| 03   | 将 `ImageMagick` 运行库解压到 `C:\wamp\base\ImageMagick` |
| 04   | 将 `C:\wamp\base\ImageMagick\bin` 加入到 `系统环境变量`  |

> 提示：`C:\wamp\base\ImageMagick\bin` 必须加入的 `系统环境变量` (httpd 无法获取 `用户环境变量`)！

## 安装 composer

composer 安装教程请查阅 [composer 快速入门](./../../PHP/01-composer快速入门.md)

## 附录

1.  php 不能动态加载 `php_curl.dll` 扩展

    答：因为 httpd 的 bin 目录下缺少 1 个 openssl 库文件 `libssh2.dll`：

    | 步骤 | 具体操作                                    |
    | ---- | ------------------------------------------- |
    | 01   | 在 php 根目录下找到 `libssh2.dll` 文件      |
    | 02   | 将 `libssh2.dll` 复制到 httpd 的 bin 目录下 |
    | 03   | 重启 httpd 服务                             |

    > 提示：亲测 `php 7.4.0` 只需要 `libssh2.dll` 库文件，[官方说明](https://www.php.net/manual/en/curl.installation.php)需要 3 个库文件

2.  安装 composer 时，报 SSL 错误问题

    报错内容如下：

    ```sh
    PHP Warning:  copy(): SSL operation failed with code 1. OpenSSL Error messages:
    error:1416F086:SSL routines:tls_process_server_certificate:certificate verify failed in Command line code on line 1
    ```

    原因分析：是 php.ini 中 `openssl.cafile=` 文件路径配置错误

    ```sh
    从 https://curl.se/docs/caextract.html 地址可以看到：
      - 该文件为openssl从Mozilla提取的CA证书

    证书文件下载地址： https://curl.se/ca/cacert.pem
    ```

    下载成功后，放置到 `base/conf` 目录下,php.ini 最下方加上如下内容：

    ```ini
    [openssl]
    openssl.cafile="C:\wamp\base\conf\cacert.pem"
    ```

3.  curl 扩展检测不通过

    -   php.ini 配置文件里，扩展没有开启

        ```ini
        ; 去掉下1行最前面的冒号
        ; extension=curl
        ```

    -   httpd 没有加载对应的文件

        ```conf
        LoadFile "${WAMP_ROOT}/base/php/libssh2.dll"
        LoadFile "${WAMP_ROOT}/base/php/libcrypto-1_1-x64.dll"
        LoadFile "${WAMP_ROOT}/base/php/libssl-1_1-x64.dll"
        ```
