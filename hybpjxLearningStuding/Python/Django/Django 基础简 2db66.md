# Django 基础简介

# 基础简介

## 1. **软件框架**

一个公司是由公司中的各部部门来组成的，每一个部门拥有特定的职能，部门与部门之间通过相互的配合来完成让公司运转起来。

一个软件框架是由其中各个软件模块组成的，每一个模块都有特定的功能，模块与模块之间通过相互配合来完成软件的开发。

软件框架是针对某一类软件设计问题而产生的。

## 2. **MVC框架**

### 2.1 MVC简介

MVC最初是由施乐公司旗下的帕罗奥多研究中心中的一位研究人员给 smalltalk语言发明的一中软件设计模式。

MVC的产生理念： **分工**。让专门的人去做专门的事。

MVC的核心思想： **解耦**。

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image2.jpeg](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image2.jpeg)

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image3.jpg](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image3.jpg)

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image4.jpg](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image4.jpg)

MVC的思想被应用在的web开发的方面，产生了**web MVC框架**。

### **2.2 Web MVC框架模块功能**

通过浏览器注册用户信息。

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image5.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image5.png)

M:Model,模型， 和数据库进行交互。

V:View,视图， 产生html页面。

C:Controller,控制器, 接收请求，进行处理，与M和V进行交互，返回应答。

## 3. **Django框架**

### 3.1 简介

Django是劳伦斯出版集团的开发人员为开发新闻内容网站而设计出来的一个软件，它遵循MVC思想，但是有自己的一个名词，叫做**MVT**。

Django遵循快速开发和DRY原则。Do not repeat yourself.不要自己去重复一些工作。

### **3.2 MVT各部分功能**

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image6.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image6.png)

M:Model,模型， 和MVC中M功能相同，和数据库进行交互。

V:View,视图， 和MVC中C功能相同，接收请求，进行处理，与M和T进行交互，返回应答。

T:Template,模板， 和MVC中V功能相同，产生html页面。

## 4. **虚拟环境**

### 4.1 概念

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image7.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image7.png)

之前安装python包的命令： sudo pip3 install 包名

包的安装路径：/usr/local/lib/python3.5/dist-packages

在同一个python环境中安装同一个包的不同版本，后安装的包会把原来安装的包覆盖掉。这样，如果同一台机器上两个项目依赖于相同包的不同版本，则会导致一些项目运行失败。

解决的方案就是：虚拟环境。

**虚拟环境是真实python环境的复制版本。**

**在虚拟环境中使用的python是复制的python,安装python包也是安装在复制的python中。**

### **4.2 安装和配置**

**安装虚拟环境的命令：**

1）`sudo pip install virtualenv -i [https://pypi.tuna.tsinghua.edu.cn/simple/](https://pypi.tuna.tsinghua.edu.cn/simple/)`  #安装虚拟环境

2）`sudo pip install virtualenvwrapper -i [https://pypi.tuna.tsinghua.edu.cn/simple/](https://pypi.tuna.tsinghua.edu.cn/simple/)` #安装虚拟环境扩展包

3）编辑家目录下面的.bashrc文件，添加下面两行。

```bash
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON='/usr/bin/python3'
# export VIRTUALENVWRAPPER_VIRTUALENV=~/virtualenvs
export VIRTUALENVWRAPPER_VIRTUALENV=~/.local/bin/virtualenv
source /usr/local/bin/virtualenvwrapper.sh
```

4）使用source .bashrc使其生效一下。

### **4.3 使用**

**创建虚拟环境命令：**

> mkvirtualenv 虚拟环境名
> 

**创建python3虚拟环境：**

> mkvirtualenv -p python3 bj11_py3
> 

**进入虚拟环境工作：**

> workon 虚拟环境名
> 

**查看机器上有多少个虚拟环境：**

> workon 空格 + 两个tab键
> 

**退出虚拟环境：**

> deactivate
> 

**删除虚拟环境：**

> rmvirtualenv 虚拟环境名
> 

虚拟环境下安装包的命令：

> pip install 包名
> 

注意：不能使用sudo pip install 包名，这个命令会把包安装到真实的主机环境上而不是安装到虚拟环境中。

**查看虚拟环境中安装了哪些python包：**

> pip list
pip freeze
> 

**安装django环境：**

> pip install django==3.2.6
> 

**拓展:**

> apt-get install 软件
> 

> pip install python包名
> 

## 5. **项目创建**

### 5.1 创建Django项目

命令：

> django-admin startproject 项目名
> 

注意：创建应用必须先进入虚拟环境。

项目目录如下：

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image8.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image8.png)

<aside>
💡 __init__.py: 说明test1是一个python包。
</aside>

<aside>
💡 settings.py: 项目的配置文件。
</aside>

<aside>
💡 urls.py: 进行url路由的配置。

</aside>

<aside>
💡 wsgi.py: web服务器和Django交互的入口。

</aside>

<aside>
💡 manage.py: 项目的管理文件。
</aside>

**5.2 创建Django应用**

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image9.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image9.png)

一个项目由很多个应用组成的，每一个应用完成一个功能模块。

创建应用的命令如下：

> python manage.py startapp 应用名
> 

注意：创建应用时需要先进入项目目录。

应用目录如下：

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image10.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image10.png)

<aside>
💡 __init__.py: 说明目录是一个Python模块。

</aside>

<aside>
💡 models.py: 写和数据库项目的内容, 设计模型类。

</aside>

<aside>
💡 views.py: ，接收请求，进行处理，与M和T进行交互，返回应答。

</aside>

<aside>
💡 定义处理函数，视图函数。

</aside>

<aside>
💡 tests.py: 写测试代码的文件。

</aside>

<aside>
💡 admin.py: 网站后台管理相关的文件。

</aside>

**5.3 应用注册**

建立应用和项目之间的联系，需要对应用进行注册。

修改settings.py中的INSTALLED_APPS配置项。

**5.4 启动项目**

运行开发web服务器命令：

> python manage.py runserver
> 

## 6. **模型类**

### 6.1 ORM

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image11.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image11.png)

django中内嵌了ORM框架，ORM框架可以将类和数据表进行对应起来，只需要通过类和对象就可以对数据表进行操作。

在Django中主要是设计类：模型类。

ORM另外一个作用：根据设计的类生成数据库中的表。

### **6.2 模型类设计**

在应用models.py中设计模型类。

必须继承与models.Model类。

1. 设计BookInfo类。
2. 设计HeroInfo类。

Models.ForeignKey可以建立两个模型类之间一对多的关系，django在生成表的时候，就会在多端的表中创建一列作为外键，建立两个表之间一对多的关系。

### 6.3 **模型类生成表**

1. 生成迁移文件

命令：python manage.py makemigrations

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image12.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image12.png)

迁移文件是根据模型类生成的。

1. 执行迁移生成表

命令：python mange.py migrate

根据迁移文件生成表。

生成表名的默认格式：

应用名_模型类名小写

### **6.4 通过模型类操作数据表**

进入项目shell的命令:

> python manage.py shell
> 

以下为在相互shell终端中演示的例子：

首先导入模型类：

> from booktest.models import BookInfo,HeroInfo
> 
1. **向booktest_bookinfo表中插入一条数据。**

```python
b = BookInfo() #定义一个BookInfo类的对象
b.btitle ='天龙八部' #定义b对象的属性并赋值
b.bpub_date = date(1990,10,11)
b.save() #才会将数据保存进数据库
```

1. **查询出booktest_bookinfo表中id为1的数据。**

```python
b = BookInfo.objects.get(id=1)
```

1. **在上一步的基础上改变b对应图书的出版日期。**

```python
b.bpub_date = date(1989,10,21)
b.save() #才会更新表格中的数据
```

1. **紧接上一步，删除b对应的图书的数据。**

```python
b.delete() #才会删除
```

1. **向booktest_heroInfo表中插入一条数据。**

```python
h = HeroInfo()
h.hname = '郭靖'
h.hgender = False
h.hcomment = '降龙十八掌'
b2 = BookInfo.objects.get(id=2)
h.hbook = b2 #给关系属性赋值，英雄对象所属的图书对象
h.save()
```

**6) 查询图书表里面的所有内容。**

```python
BookInfo.objects.all()
HeroInfo.objects.all()
```

### **6.5 关联操作**

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image13.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image13.png)

1) 查询出id为2的图书中所有英雄人物的信息。

```python
b = BookInfo.objects.get(id=2)
b.heroinfo_set.all() #查询出b图书中所有英雄人物的信息
```

## **7. 后台管理**

1. 本地化

> 语言和时区的本地化。
> 
> 
> 修改settings.py文件。
> 
1. 创建管理员

> 命令：python manage.py createsuperuser
> 
1. 注册模型类

> 在应用下的admin.py中注册模型类。
> 
> 
> 告诉djang框架根据注册的模型类来生成对应表管理页面。
> 
> ```python
> b = BookInfo()
> str(b) __str__
> ```
> 
1. 自定义管理页面

> 自定义模型管理类。模型管理类就是告诉django在生成的管理页面上显示哪些内容。
> 

## 8. **视图**

在Django中，通过浏览器去请求一个页面时，使用视图函数来处理这个请求的，视图函数处理之后，要给浏览器返回页面内容。

### **8.1 视图函数的使用**

1) 定义视图函数

视图函数定义在views.py中。

例：

```python
def index(request):
#进行处理...
		return HttpResponse('hello python')
```

视图函数必须有一个参数request，进行处理之后，需要返回一个HttpResponse的类对象，hello python就是返回给浏览器显示的内容。

2) 进行url配置

url配置的目的是让建立url和视图函数的对应关系。url配置项定义在urlpatterns的列表中，每一个配置项都调用url函数。

url函数有两个参数，第一个参数是一个正则表达式，第二个是对应的处理动作。

配置url时，有两种语法格式：

- url(正则表达式，视图函数名)
- url(正则表达式，include(应用中的urls文件))

工作中在配置url时，首先在项目的urls.py文件中添加配置项时，并不写具体的url和视图函数之间的对应关系，而是包含具体应用的urls.py文件，在应用的urls.py文件中写url和视图函数的对应关系。

### **8.2 url匹配的过程**

在项目的urls.py文件中包含具体应用的urls.py文件，应用的urls.py文件中写url和视图函数的对应关系。

![Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image15.png](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/image15.png)

当用户输入如 http://127.0.0.1:8000/aindex时，去除域名和最前面的/，剩下aindex，拿aindex字符串到项目的urls文件中进行匹配，配置成功之后，去除匹配的a字符，那剩下的index字符串继续到应用的urls文件中进行正则匹配，匹配成功之后执行视图函数index，index视图函数返回内容hello python给浏览器来显示。

## 9. **模板**

模板不仅仅是一个html文件。

### 9.1 **模板文件的使用**

1. 创建模板文件夹
2. 配置模板目录

![Untitled](Django%20%E5%9F%BA%E7%A1%80%E7%AE%80%202db66/Untitled.png)

1. 使用模板文件
    - 加载模板文件
        - 去模板目录下面获取html文件的内容，得到一个模板对象。
    - 定义模板上下文
        - 向模板文件传递数据。
    - 模板渲染
        - 得到一个标准的html内容。

### **9.2 给模板文件传递数据**

<aside>
💡 模板变量使用：{{ 模板变量名 }}

</aside>

<aside>
💡 模板代码段：{%代码段%}

</aside>

<aside>
💡 for循环：{% for i in list %}

</aside>

<aside>
💡 list不为空时执行的逻辑：{% empty %}

</aside>

<aside>
💡 list为空时执行的逻辑：{% endfor %}

</aside>

## 10. **案例完成**

编码之前的准备工作：

1. 设计出访问页面的url和对应的视图函数的名字，确定视图函数的功能。
2. 设计模板文件的名字。

以下为案例中的简单设计过程：

1. 完成图书信息的展示：
    1. 设计url，通过浏览器访问 [http://127.0.0.1:8000/books 时显示图书信息页面](http://127.0.0.1:8000/books/%20%E6%97%B6%E6%98%BE%E7%A4%BA%E5%9B%BE%E4%B9%A6%E4%BF%A1%E6%81%AF%E9%A1%B5%E9%9D%A2)。
    2. 设计url对应的视图函数show_books。
       
        查询出所有图书的信息，将这些信息传递给模板文件。
        
    3. 编写模板文件show_books.html。
       
        遍历显示出每一本图书的信息。
    
2. 完成点击某本图书时，显示出图书里所有英雄信息的页面。
    1. 设计url,通过访问http://127.0.0.1:8000/books/数字时显示对应的英雄信息页面。
       
        这里数字指点击的图书的id。
        
    2. 设计对应的视图函数detail。
       
        接收图书的id,根据id查询出相应的图书信息，然后查询出图书中的所有英雄信息。
        
    3. 编写模板文件detail.html。