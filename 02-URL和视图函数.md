# URL和视图函数

## URL结构

- URL，统一资源定位符，表示资源的地址
- 一般语法格式：`protocol://hostname[:port]/path[?query][#fragement]`

- protocol 协议：`http://` `https://` 安全的HTTPS `file:/// `本地计算机的文件
- hostname 主机名：存放资源的服务器的域名系统主机名、域名、IP地址
- port 端口：http默认走80
- path 路由地址：目录或文件的地址，路由决定了服务器端如何处理该请求
- query 带?号：可选，用于给动态网页传递参数，可有多个参数，用&隔开，每个参数采用键值对的形式，如`?menuID=435435&version=5465`
- fragment 带#号：字符串，用于指定网络资源的片段，可以直接定位到某一名词，如 `#subject`，有点像个书签

## django如何处理URL请求

举例：浏览器地址栏访问：http://127.0.0.1:8000/page/2003/

- django从配置文件中根据ROOT_URLCONF找到主路由文件，默认情况下，该文件在项目同名目录下的urls里面，例如 `mysite1/mysite1/urls.py`
- django加载主路由文件中的urlpattern变量，其包含很多路由的数组
- 依次匹配urlpatterns中的path，匹配到的第一个合适的，中断后续匹配
- 匹配成功：嗲用对应的视图函数处理请求，返回响应
- 匹配失败：返回404响应

主路由 urls.py 样例

```python
from django.contrib import admin
from django.urls import path
from . import views

urlpatterns = [
  	path(('admin/', admin.site.urls),
    path('page/2003/', views.page_2003), # (path, 视图函数)
    path('page/20O4/', views.page_2004),
]
```

## 视图函数

视图函数用于接收一个浏览器请求并通过HttpResponse对象返回响应的函数

此函数可以接收浏览器请求并根据业务逻辑返回响应的响应内容给浏览器

语法：

```python
def xxx_view(request [, 其他参数]):
	return HttpResponse对象
```

视图函数样例

```python
from django.http import HttpResponse
def page1_view(request):
    html = "<h1>这是第一个页面</h1>"
    return HttpResponse(html)
```

# 路由配置

- 访问路由的过程：settings.py --> ROOT_URLCONF --> urls.py --> urlpatterns --> path() --> view

## path函数

- path()
- 导入：`from django.urls import path`
- 语法：`path(route, views, name=None)`
- 参数：
  - route：字符串类型，匹配的请求路径
  - views：指定路径对应的视图处理函数的名称，不是调用，不要加括号()
  - name：为地址起别名，在模板中地址反向解析时使用

小练习

```python
# 练习:建立一个小网站
# 输入网址:http://127.0.0.1:8000,在网页中输出：这是我的首页 
# 输入网址:http://127.0.0.1:8000/page/1,在网页中输出：这是编号为1的网页
# 输入网址:http://127.0.0.1:8000/page/2,在网页中输出：这是编号为2的网页

# --urls.py--
path('',views.index_view)
path('page/1',views.page1_view)
path('page/2',views.page2_view)

# --views.py--
def index_view(request):
    html = '这是我的首页 '
    return HttpResponse(html)

def page1_view(request):
    html = '这是编号为1的网页 '
    return HttpResponse(html)

def page2_view(request):
    html = '这是编号为2的网页 '
    return HttpResponse(html)
```

思考：如何建立100个网页？

## path转换器

- 语法：`<转换器类型：自定义名>`
- 作用：若转换器类型匹配到对应类型的数据，则将数据按照关键字传参的方式传递给视图函数
- 例子：`path('page/<int:page>', views.xxx)`

转换器类型

| 类型    | 作用                                                    | 样例                      | 匹配到                 |
| ------- | ------------------------------------------------------- | ------------------------- | ---------------------- |
| **str** | 匹配除了 / 之外的非空字符串                             | `v1/users/<str:username>` | /v1/users/guoxiaoniao  |
| **int** | 匹配0或者任何正整数，返回int                            | `page/<int:page>`         | /page/100              |
| slug    | 匹配任意由ASCII字母或数字以及连字符、下划线组成的短标签 | `detail/<slug:sl>`        | /detail/this-is-django |
| path    | 匹配非空字段，包括路径分隔符 /                          | `v1/users/<path:ph>`      | /v1/goods/a/b/c        |

重写上面那个小练习：

```python
# --urls.py--
path('page/<int:pg>',views.pagen_view)

# --views.py--
def pagen_view(request, pg):
    html = '这是编号为%s的网页!'%(pg)
    return HttpResponse(html)
```

小练习：小计算器

定义一个路由的格式为：http//127.0.0.1:8000/整数/操作字符串[add/sub/mul]/整数

从路由中提取数据，做响应的操作后返回给浏览器

效果如下：127.0.0.1:8000/100/add/200 

页面显示结果：300

```python
# --urls.py--
path('<int:num1>/<str:ops>/<int:num2>',views.result_view)

# --views.py--
def pagen_view(request, num1, ops, num2):
    
    if op not in ['add', 'sub', 'mul']:
        return HttpResponse('Your ops is wrong')
   
	res = 0
    if ops == 'add':
        res = num1 + num2
    elif ops == 'sub':
        res = num1 - num2
    elif ops == 'nul':
        res = num1 * num2
    
    html = '结果为：%s' % (res)
    return HttpResponse(html)
```

## re_path()函数

比path()函数更高级，更精准

- 在url匹配的过程中，可以使用regex进行精确匹配
- 语法：`re_path(reg, view, name=xxx)`
- 正则表达式为命名分组模式 `?P<name>pattern`
- 匹配提取参数后用关键字传参方式传递给视图函数

样例

```python
urlpatterns = [
    path('admin/', admin.site.urls)
    re_path(r'^(?P<x>\d{1,2})/(?P<op>\w+)/(?P<y>\d{1,2})$', views.cal_view)
]

# 可以‘精确’限制匹配数字的位数
```

小练习

```shell
# 访问地址
http://127.0.0.1:8000/birthday/d{4}/d{1,2}/d{1,2}
http://127.0.0.1:8000/birthday/d{1,2}/d{1,2}/d{4}
最终输出：生日为：xxxx年xx月xx日
```

