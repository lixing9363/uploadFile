## Django反向解析

> 通过视图函数的名称来解析出视图函数的访问路由

#### 1.用途

当视图越来越多的时候,可能会觉得之前配置的路由不够准确,如果修改配置的路由,那么之前在页面中填写的超链接的网址都需要修改,量大并且可能漏改. 如果通过反向解析,则直接通过视图函数来定位指定的路由,只要不修改urls.py中配置的路由名称,无论路由怎么修改,都是直接解析出来修改后的路由,是动态生成路由的方法.

#### 2.模板中超链接的解析

##### 2.1配置

- 创建项目

```shell
django-admin startproject mysite
cd mysite
python manage.py startapp test_app
```

- 配置setting.py
  1. 配置TEMPLATES : os.path.join(BASE_DIR, 'templates')
  2. 配置INSTALLED_APPS :"test_app"

- 配置主从路由

  1. 主路由

  ```python
  # 要配置反向解析,必须要配置namespace命名空间
  url('^test/', include("test_app.urls", namespace="test_app")),
  ```

  2. 子路由

  ```python
  from test_app import views
  
  # 主路由中include配置了namespace,必须要配置app_name
  app_name = "test_app"
  
  urlpatterns = [
      url('^$', views.test_view1),
      # 要反向解析的url需要配置name属性
      url('^test_url$', views.test_view2, name='test2'), 
  ]
  ```

- 配置views.py

  ```python
  def test_view1(request):
      return render(request, "index.html")
  
  def test_view2(request):
      pass
  ```

- 在templates文件夹中创建index.html

  ```html
  <body>
  普通链接：<a href="/fan2/">普通fan2</a>
  <hr>
  反向解析：<a href="{%url 'test_app:test2'%}">反向解析fan2</a>
  </body>
  ```

##### 2.2演示

- 浏览器打开http://127.0.0.1:8000/test/

  查看网页element可以看到

  ```html
  <body>
  普通链接：<a href="/fan2/">普通fan2</a>
  <hr>
  反向解析：<a href="/test/test_url">反向解析fan2</a>
  </body>
  ```

#### 3.视图函数中的重定向

> 重定向也可以通过反向解析定向

##### 3.1不使用反向解析重定向

- 子路由添加

```python
url('^test_url$', views.test_view2, name='test2'),
url('^redirect$', views.redirect_view),
```

- views.py

```python
def test_view2(request):
    return HttpResponse("这是要重定向后的页面")

def redirect_view(request):
    return redirect(reverse('/test/test_url'))
```

> 访问http://127.0.0.1:8000/test/redirect,可以跳转到http://127.0.0.1:8000/test/test_url

如果后来发现,'^test_url$'路由不够准确,需要修改为test_url1111

```python
# url('^test_url$', views.test_view2, name='test2'),
url('^test_url1111$', views.test_view2, name='test2'),
```

再次访问http://127.0.0.1:8000/test/redirect 会发现还是跳转到了http://127.0.0.1:8000/test/test_url

##### 3.2 使用反向解析重定向

- 修改views.py

```python
def redirect_view(request):
    return redirect('test_app:test2')
```

> 再次访问http://127.0.0.1:8000/test/redirect 就会重定向到http://127.0.0.1:8000/test/test_url1111

#### 4.带参数的反向解析

- 修改urls.py

```python
url('^$', views.test_view1),
url(r'^test_url(\d+)_(\d+)$', views.test_view2, name='test2'),
```

- 修改views.py

```python
def test_view1(request):
    return render(request, "index.html")

def test_view2(request, a, b):
    return HttpResponse(a + b)
```

- 修改index.html

```html
<body>
普通链接：<a href="/fan2/">普通fan2</a>
<hr>
反向解析：<a href="{%url 'test_app:test2' 2 3%}">反向解析fan2</a>
</body>
```

- 打开http://127.0.0.1:8000/test/ 可以看到

```html
<body>
普通链接：<a href="/fan2/">普通fan2</a>
<hr>
反向解析：<a href="/test/test_url2_3">反向解析fan2</a>
</body>
```

> 重定向的格式传递方式为

```python
return redirect(reverse('test_app:test2', args=(2,3)))
```

#### 5.名称参数

- 修改urls.py

```python
url('^$', views.test_view1),
url(r'^test_url(?P<a>\d+)_(?P<b>\d+)$', views.test_view2, name='test2'),
```

- 修改views.py

```python
def test_view1(request):
    return render(request, "index.html")

def test_view2(request, a, b):
    return HttpResponse(a + b)
```

- 修改index.html

```html
<body>
普通链接：<a href="/fan2/">普通fan2</a>
<hr>
反向解析：<a href="{%url 'test_app:test2' a=2 b=3%}">反向解析fan2</a>
</body>
```

- 打开http://127.0.0.1:8000/test/ 可以看到

```html
<body>
普通链接：<a href="/fan2/">普通fan2</a>
<hr>
反向解析：<a href="/test/test_url2_3">反向解析fan2</a>
</body>
```

> 重定向的格式传递方式为

```python
return redirect(reverse('test_app:test2', kwargs={'a': 2, 'b': 3}))
```

