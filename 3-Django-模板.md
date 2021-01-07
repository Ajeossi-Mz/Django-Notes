# Django-模板

### 模板和模板引擎

- 模板具有一定的格式或骨架，可以动态的生成HTML
- 模板引擎决定以何种方式组织代码
- 一个项目可以有一个或者是多个模板引擎
- Django支持两种模板引擎：
  - DTL（Django Template Language）是django原生的模板系统
  - Jinjia2

### 渲染机制

- 步骤一：从磁盘读取模板文件(get_template)
- 步骤二：选择合适的模板引擎(select_template)
- 步骤三：将制定内容对模板进行渲染(render)

### 配置

在工程中创建模板目录templates,在settings.py配置文件中修改**TEMPLATES**配置项的DIRS值：

- BACKEND：模板引擎配置
- DIRS：         模板引擎按列表顺序搜索这些目录以查找模板源文件
- APP_DIRS：决定模板引擎是否应该进入每个已安装的应用中查找模板（记得在settings.py中INSTALLED_APPS注册应用）

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',  #模板引擎的配置
        'DIRS': [os.path.join(BASE_DIR, 'templates')],  # 此处修改
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',   # 因此可以用到request中的一些变量
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
    {
        'BACKEND': 'django.template.backends.jinja2.Jinja2',  # jinja2配置
        'DIRS': [os.path.join(BASE_DIR, 'jinjia2')],
        'APP_DIRS':True,
        'OPTIONS':{
            'environment': 'jinja2_env.environment',# 此处修改
            'context_processors':[
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

### 模板查找的顺序

先按照settings.py中的TEMPLATES配置顺序查找，然后接着先根目录后模块目录

### 渲染Python中的变量

语法：{{ variable }}     变量名称中不能有空格或标点符号，不能以“_”开头

```python
def index(request):
    context = {
        'city': '北京',
        'adict': {
            'name': '西游记',
            'author': '吴承恩'
        },
        'alist': [1, 2, 3, 4, 5]
    }
    return render(request, 'index.html', context)

"""
# URL解析
# URL标签的使用
{% url 'url_name' params %}
# static静态文件URL解析
{% load static %}
<img src="{% static "images/cat.jpg" %}" alt="Cat">
"""
```

**具体语法参考jinja2：**

- **http://www.ainoob.cn/docs/jinja2/index.html **
- **http://docs.jinkan.org/docs/jinja2/**

### DTL与Jinja2的使用区别

```
注意1：
变量名称中不能有空格或标点符号,下面的语法在DTL中不被支持
{{ object["a.b"] }}
{{ object["a b"] }}

注意2：
类中的成员方法调用不需要()，也不支持参数传递
class A():
	def display():
		return 'hello'
a = A()
{{ a.display }}

注意3：
循环变量forloop, Jinja2中为loop
List为空：{% empty %}，Jinja2中为{% else %}
循环中的再循环
	DTL: {% cycle 'odd' 'even' %}
	Jinja2: {{ loop.cycle('odd', 'even') }}
DTL不支持continue和break

注意4：
DTL
{% url 'url_name' params %}
Jinja2
{{ url_for('index') }}

```

### 模板的抽象和继承 -- extend

```
步骤一：将可变的部分圈出来(base.html)
{% block sidebar %}
	<!--菜单栏的内容 -->
{% endblock %}

步骤二：继承父模板
{% extends "base.html" %}

步骤三：填充新的内容(index.html)
{% extends "base.html" %}
{% block sidebar %}
	<!-- 新的菜单栏的内容 -->
{% endblock %}

步骤四：复用父模板的内容(可选)
{% extends "base.html" %}
{% block sidebar %}
    {{ block.super }}
    <!-- 新的菜单栏的内容 -->
{% endblock %}
```

### 在模板中添加公共部分  -- include

```
步骤一：将可变的部分拆出来(footer.html)
<footer>
	这是页脚公共的部分
</footer>

步骤二：将拆出来的部分包进来（index.html)
{% extends "base.html" %}
{% block content %}
	<!– 页面主要内容区域-->
	{# 公用的footer #}
	{% include "footer.html" %}
{% endblock %}
```

### 模板过滤器

过滤器：对变量进行特殊处理后再渲染

```
过滤器语法：{{ value|filter_name: params }}
实例：使用过滤器将字母大写 {{ value | upper }}

# 内置的过滤器
默认值显示
	{{ value|default:"" }}
	{{ value|default_if_none:“无" }}
数字四舍五入显示
	{{ value | floatformat：3 }}
日期对象格式化
	{{ value|date:"D d M Y" }}
时间对象格式化
	{{ value|time:"H:i" }}
富文本内容转义显示
	{{ value|safe }}
字符串截取
	{{ value|truncatechars:9 }}
	{{ value|truncatechars_html:9 }}
	{{ value|truncatewords:2 }}

```

### 自定义过滤器

```
# 步骤一：在app模块目录下新建包templatetags
    polls/
    	__init__.py
    	models.py
    	templatetags/
    		__init__.py
    		poll_extras.py
    	views.py
   
# 步骤二：实现过滤器poll_extras.py
    from django import template
    register = template.Library()
    def warning(value):
        """ 将第一个字符变红 """
        return '<span class="red">' + value[0] + '</span>+ value[1:]

# 步骤三：注册过滤器
    # 方式一：注册过滤器
    register.filter('warning', warning)
    # 方式二：注册过滤器
    @register.filter(name='warning')
    def warning(value):
        pass
        
步骤四：在模板中使用过滤器
    {% load poll_extras %}
    {{ value| warning }}	
```



