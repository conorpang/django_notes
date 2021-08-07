# Django项目结构

## 创建项目

- 成功安装django后，虚拟机终端会有django-admin命令
- 执行`django-admin startproject 项目名`，即可创建出对应项目文件夹
- 例如终端执行 `django-admin startporject mysite1` 则创建出mysite1项目

## 启动服务

- 终端cd进入到项目文件夹
- 执行`python3 manage.py runserver` 启动django服务，前台启动，默认监听8000端口
- 浏览器访问`127.0.0.1:8000` 可以看到启动页面
- 若想切换端口：`python3 manage.py runserver 端口号`

## 关闭服务

- 方式1：（前台启动时）在runserver启动终端下 `ctrl+C`
- 方式2：（后台启动时）在其他终端下，执行 `sudo lsof -i:8000` 查询django的进程PID (LISTEN状态的)，执行 `kill -9 PID`

## 启动常见问题

- 启动时报错：Error: That port is already in use 解决方案：扫描冲突端口PID杀掉，或者换一个端口号

## 结构解析

```shell
mysite1/
    manage.py
    mysite1/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

### manage.py

```python
python manage.py runserver # 启动服务
python manage.py startapp # 创建应用
python manage.py migrate # 数据库迁移
...

python manage.py # 可以列出所有的django子命令
```

### 项目同名文件夹

- `__init__` python包的初始化文件
- `wsgi.py` WEB服务网关的配置文件，django正式启动时，需要用到
- `urls.py` 项目的主路由配置，HTTP请求进入django时，优先调用该文件
- `settings.py` 项目的配置文件，包含项目启动时需要的配置

## settings.py

- 包含了django项目启动的所有配置项
- 配置项分为**公有配置**（记住高频使用的即可）和**自定义配置**
- 配置项格式： `BASE_DIR = 'xxx'` 注意名字一定是大写
- 公有配置：django官方提供的基础配置
- https://docs.djangoproject.com/en/2.2/ref/settings/

### 公有配置

```python
BASE_DIR = ... # 项目文件夹 mysite1 根目录，绝对路径

DEBUG = True # 项目的启动模式
# True 调试模式
# 1. 检测代码改动后，like重启服务
# 2. 报错页面
# False 正式启动模式/上线模式

ALLOWED_HOSTS = [] # 请求头host头，测试时一般按默认配置
# 在0.0.0.0:port 启动django，然后把内网其他地址加入allowed_host，即可实现那个地址访问django

INSTALLED_APPS = [] # 指定当前项目中安装的应用列表
MIDDLEWARE = [] # 用于注册中间件

ROOT_URLCONF = 'mysite1.urls' # 主路由配置，默认不用管
TEMPLATES = [] # 模板的配置信息
DATABASES = {} # 数据库的配置信息

LANGUAGE_CODE = 'en-us' # 中文为zh-Hans # 语言配置信息
TIME_ZONE = 'UTC' # 东八区可以改为 Asia/Shanghai # 指定当前服务器端时区
```

### 自定义配置

- 配置建议：名字尽量个性化，以防覆盖掉公有配置
- 例如：`ALIPAY_KEY = 'xxx'`
- settings.py中的所有配置项，都可以按需在代码中引入
- `from django.conf import settings`



