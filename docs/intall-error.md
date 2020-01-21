---
id: install-error
title: 安装问题
---

## 常见安装问题

### 登录报错 `请求失败: 504 Gateway Timeout`
请确保 api 服务是否启动，如果已启动则可以通过控制台查看是否监听在 `8000` 端口，如果不是 `8000` 端口可以改为 `8000` 端口或者修改前端项目的
`spug/spug_web/src/setupProxy.js` 文件中的 `target` 值为你的 api 服务的监听地址和端口。

### 登录报错 `Exception: Error 61 connecting to 127.0.0.1:6379. Connection refused.`
需要安装 `Redis`，如果安装的 `Redis` 不是监听在 `127.0.0.1` 需要修改配置文件 `spug_api/spug/settings.py`
指定 `Redis` 的 Host，配置中的 `CACHES` 和 `CHANNEL_LAYERS` 均使用了 `Redis`。

### 如何配置使用带密码的 `Redis` 服务？
假设 `Redis` 密码为 `foo123`，则需要更改以配置文件 `spug_api/spug/settings.py` 或者 `overrides.py` 如下内容
```shell script
vi spug_api/spug/settings.py

CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://:foo123@127.0.0.1:6379/1",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
}

CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": ["redis://:foo123@127.0.0.1:6379/0"],
        },
    },
}
```

### 批量执行的任务卡住无法看到执行输出
批量执行功能需要启动额外服务，通过以下命令启动，以下操作命令基于 [标准安装](/docs/install) 文档的环境
```shell script
cd /data/spug/spug_api
source venv/bin/activate
python manage.py runworker ssh_exec
```

### 任务计划模块添加的任务不会执行
任务计划功能需要启动额外的服务，通过以下命令启动，以下操作命令基于 [标准安装](/docs/install) 文档的环境
```shell script
cd /data/spug/spug_api
source venv/bin/activate
python manage.py runscheduler
```

### 监控中心模块添加的监控任务不会执行
监控中心功能需要启动额外的服务，通过以下命令启动，以下操作命令基于 [标准安装](/docs/install) 文档的环境
```shell script
cd /data/spug/spug_api
source venv/bin/activate
python manage.py runmonitor
```

### `macOS` 如何使用 `Mysql` 替代默认的 `Sqlite` 数据库？
需要安装 `mysqlclient` 数据库驱动库，可通过以下步骤安装
```shell script
brew install mysql-client
export PATH="/usr/local/opt/mysql-client/bin:$PATH"
export LDFLAGS="-I/usr/local/opt/openssl/include -L/usr/local/opt/openssl/lib"
pip install mysqlclient
```