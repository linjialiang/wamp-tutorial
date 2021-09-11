# MariaDB 操作篇

MariaDB 是 MySQL 的分支版本，各大 Linux 发行版镜像源都用 MariaDB 替代 MySQL 了

## my.ini

my.ini 是 MariaDB 默认配置文件名，可以有多种路径选择，具体如下：

### my.ini 文件路径

MariaDB 的根配置文件，只能读取 1 个，如果有多个，可以做为子配置文件，从根配置文件里加载

-   c:\my.ini

    ```text
    - Win下 MariaDB 全局配置文件
    - 所有的 mysqld.exe 被启动时都可以读取
    ```

-   c:\wamp\mariadb\my.ini

    ```text
    - 对应的 mysqld.exe 服务被启动时可读取
    - 这个是我们预先写好的配置文件，在安装MariaDB时被引入
    ```

-   c:\wamp\web\data\my.ini

    ```text
    - 对应的 mysqld.exe 服务被启动时可读取
    - MariaDB 安装指令，默认会生成此配置文件
    ```

### my.ini 优先级

如果 win 系统上有多个 MariaDB 配置文件，MariaDB 会按优先级依次读取，直到读取到 1 个配置文件后，就会结束继续读取

如果 win 系统上没有 MariaDB 配置文件，那么 MariaDB 将无法启动

my.ini 优先级具体如下：

1. --defaults-file="path"

    ```text
    - 由 --defaults-file 指定的配置文件
    - 优先级：1，具体最高优先权
    - 安装系统服务上
        1. 指定了该参数，没有找到该文件就会报错，其它配置文件自动失效
        2. 没有指定该参数，将查看下1个
    ```

2. c:\my.ini

    ```text
    - 系统盘根目录下的配置文件
    - 优先级： 2
    - 如果该文件不存在，将继续查看下1个
    ```

3. c:\wamp\mariadb\my.ini

    ```text
    - MariaDB 根目录下的配置文件
    - 优先级：3
    - 如果该文件不存在，将继续查看下1个
    ```

4. c:\wamp\web\data\my.ini

    ```text
    - MariaDB 数据目录下的配置文件
    - 优先级：4
    - 如果该文件不存在，系统将报错
    ```

## 初始化数据

MariaDB 默认没有 data 数据目录，需要我们自己进行初始化，这里我们推荐使用 mysql_install_db.exe 来管理

mysql_install_db.exe 会自动生成系统服务，如果只想生成 data 数据，可以使用 mysqld 相关指令

### 入门级操作

初学者不想使用指令，可以如下操作

| 步骤 | 具体操作                                    |
| ---- | ------------------------------------------- |
| 1    | 双击 mysql_install_db.exe 生成 datadir 目录 |
| 2    | 默认路径：c:\wamp\base\mariadb\data         |

### 通用初始化操作

可以将 c:\wamp\base\mariadb\bin 加入环境变量中，在任意目录下执行

也可以先 进入 c:\wamp\base\mariadb\bin 目录下，在执行

-   不指定配置文件

    指定 data 目录、初始密码

    ```cmd
    > cd c:\wamp\base\mariadb\bin
    > mysql_install_db.exe --datadir=c:/wamp/web/data --password=123456
    ```

-   指定配置文件

    指定 data 目录、初始密码、加载预先写好的配置文件信息、设置服务名称

    ```cmd
    > cd c:\wamp\base\mariadb\bin
    > mysql_install_db.exe --datadir=c:/wamp/web/data --password=123456 --config=c:/wamp/base/mariadb/my.ini --service=mariadb
    ```

-   --config 指令

    该指令是拷贝指定配置文件到 data 目录下，即：

    安装服务上增加的是： "--defaults-file=C:\wamp\web\data\my.ini"

### 必备文件

### 可移除文件

data 目录下移除文件列表如下：

-   ${hostname}.err

    错误日志，由 my.ini 重新指定

-   test 目录

    一个测试用的数据库，部署环境建议移除掉

    新版本：初始化时已经不存在

-   my.ini

    如果不是 --defaults-file 指定配置文件，可以移除

    新版本：是 --defaults-file 指定文件，不可移除
