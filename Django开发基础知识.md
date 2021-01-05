# Django开发基础知识

### Django项目创建

方式一：使用命令行生成模板

```shell
django-admin startproject my_project
```

方式二：pycharm创建

### Django项目结构

```
|-- my_project/		#项目目录
|  |--__init__.py	#包的入口文件
|  |-- settings.py	#项目配置文件
|  |-- urls.py		#url访问地址配置文件
|  |-- wsgi.py		#部署配置
|  |-- asgi.py		#部署配置
|-- manage.py		#命令行管理工具
```

### 更换端口

```
python manage.py runserver 8080
```

​	如果你想要修改服务器监听的IP，在端口之前输入新的。比如，为了监听所有服务器的公开IP（这你运行 Vagrant 或想要向网络上的其它电脑展示你的成果时很有用），使用：

```
python manage.py runserver 0.0.0.0:8000
```

**会自动重新加载的服务器runserver**

### 创建应用

在 Django 中，每一个应用都是一个 Python 包，并且遵循着相同的约定。请确定你现在处于 `manage.py` 所在的目录下，然后运行这行命令来创建一个应用

```
python manage.py startapp polls
```

这将会创建一个 `polls` 目录，它的目录结构大致如下：

```
|-- polls/
|  |--__init__.py
|  |--admin.py
|  |--apps.py
|  |-- migrations/
|  |    |--__init__.py
|  |--models.py
|  |--tests.py
|  |--views.py
```