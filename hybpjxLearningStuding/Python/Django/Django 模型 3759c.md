# Django 模型

# 模型

## 1. **Django ORM**

![Untitled](Django%20%E6%A8%A1%E5%9E%8B%203759c/Untitled.png)

> O(objects):类和对象。
> 

> R(Relation):关系，关系数据库中的表格。
> 

> M(Mapping):映射。
> 

**Django ORM框架的功能：**

1. 建立模型类和表之间的对应关系，允许我们通过面向对象的方式来操作数据库。
2. 根据设计的模型类生成数据库中的表格。
3. 通过方便的配置就可以进行数据库的切换。

## 2. **Django数据库配置**

### 2.1 mysql命令回顾

<aside>
💡 登录mysql数据库:mysql –uroot –p

</aside>

<aside>
💡 查看有哪些数据库:show databases

</aside>

<aside>
💡 创建数据库:create database test2 **charset=utf8; #切记:指定编码**

</aside>

<aside>
💡 使用数据库：use test2;

</aside>

<aside>
💡 查看数据库中的表：show tables;

</aside>

### **2.2 Django配置使用mysql数据库**

修改settings.py中的DATABASES。

![Django%20%E6%A8%A1%E5%9E%8B%203759c/image18.png](Django%20%E6%A8%A1%E5%9E%8B%203759c/image18.png)

注意：django框架不会自动帮我们生成mysql数据库，所以我们需要自己去创建。

### **2.3 切换mysql数据库之后不能启动服务器**

需要安装操作mysql数据库的包，python2环境和python3环境有以下区别。

- **python2需要安装mysql-python:**
    
    > pip install mysql-python
    > 
- **python3需要安装pymysql**:
    
    > pip install pymysql
    > 
    
    python3中安装好pymysql，需要在test2/__init__.py中加如下内容：
    
    ```python
    import pymysql
    pymysql.install_as_MySQLdb()
    ```
    

## 3. **复习案例**

1. 设计模型类并生成表
    1. 设计BookInfo,增加属性bread和bcomment，另外设置软删除标记属性isDelete。
    2. 设计HeroInfo类，增加软删除标记属性isDelete。
        
        软删除标记：删除数据时不做真正的删除，而是把标记数据设置为1表示删除，目的是防止重要的数据丢失。
        
2. 编写视图函数并配置URL。
3. 创建模板文件。
    
    **拆解功能：**
    
4. 图书信息展示页。
5. 设计url，通过浏览器访问 [http://127.0.0.1:8000/index时显示图书信息页面](http://127.0.0.1/index/%20%E6%97%B6%E6%98%BE%E7%A4%BA%E5%9B%BE%E4%B9%A6%E4%BF%A1%E6%81%AF%E9%A1%B5%E9%9D%A2)。
6. 设计url对应的视图函数index。

> 查询出所有图书的信息，将这些信息传递给模板文件。
> 
1. 编写模板文件index.html。

> 遍历显示出每一本图书的信息并增加新建和删除超链接。
> 
> 
> 2）图书信息新增。
> 
> a）设计url，通过浏览器访问 [http://127.0.0.1:8000/create时向数据库中新增一条图书信息](http://127.0.0.1:8000/create/%E6%97%B6%E5%90%91%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E6%96%B0%E5%A2%9E%E4%B8%80%E6%9D%A1%E5%9B%BE%E4%B9%A6%E4%BF%A1%E6%81%AF)。
> 
> b) 设计url对应得视图函数create。
> 
> ![Django%20%E6%A8%A1%E5%9E%8B%203759c/image19.png](Django%20%E6%A8%A1%E5%9E%8B%203759c/image19.png)
> 
> 页面重定向：服务器不返回页面，而是告诉浏览器再去请求其他的url地址。
> 
> 3）图书信息删除。
> 
> a）设计url，通过浏览器访问 [http://127.0.0.1:8000/delete数字删除数据库中对应的一条图书数据](http://127.0.0.1:8000/delete%E6%95%B0%E5%AD%97%E5%88%A0%E9%99%A4%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E5%AF%B9%E5%BA%94%E7%9A%84%E4%B8%80%E6%9D%A1%E5%9B%BE%E4%B9%A6%E6%95%B0%E6%8D%AE)。
> 
> 其中数字是点击的图书的id。
> 
> b)设计url对应的视图函数delete。
> 
> 获取图书的id,进行删除。
> 

## 4. **字段属性和选项**

### 4.1 模型类属性命名限制

1）不能是python的保留关键字。

2）不允许使用连续的下划线，这是由django的查询方式决定的。

3）定义属性时需要指定字段类型，通过字段类型的参数指定选项，语法如下：

属性名=models.字段类型(选项)

### **4.2 字段类型**

使用时需要引入django.db.models包，字段类型如下：

[字段类型](Django%20%E6%A8%A1%E5%9E%8B%203759c/%E5%AD%97%E6%AE%B5%E7%B1%BB%E5%9E%8B%20af425.csv)

### 4.3 字段选项

通过选项实现对字段的约束，选项如下：

[选项](Django%20%E6%A8%A1%E5%9E%8B%203759c/%E9%80%89%E9%A1%B9%202eba4.csv)

对比：null是数据库范畴的概念，blank是后台管理页面表单验证范畴的。

**经验:**

当修改模型类之后，如果添加的选项不影响表的结构，则不需要重新做迁移，商品的选项中default和blank不影响表结构。

**参考文档:**

**[http://python.usyiyi.cn/translate/django_182/index.html](http://python.usyiyi.cn/translate/django_182/index.html)**

## 5. **查询**

### 5.1 mysql的日志文件

mysql.log是mysql的日志文件，里面记录的对MySQL数据库的操作记录。默认情况下mysql的日志文件没有产生，需要修改mysql的配置文件，步骤如下：

1. 使用下面的命令打开mysql的配置文件，去除68,69行的注释，然后保存。

> sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
> 
1. 重启mysql服务，就会产生mysql日志文件。

> sudo service mysql restart
> 
1. 打开MySQL的日志文件。

> /var/log/mysql/mysql.log 是mysql日志文件所在的位置。
> 

使用下面的命令可以实时查看mysql的日志文件:

> sudo tail -f /var/log/mysql/mysql.log
> 

### **5.2 查询函数**

通过模型类.objects属性可以调用如下函数，实现对模型类对应的数据表的查询。

[ 查询函数](Django%20%E6%A8%A1%E5%9E%8B%203759c/%E6%9F%A5%E8%AF%A2%E5%87%BD%E6%95%B0%209635f.csv)

**get示例**：

例：查询图书id为3的图书信息。

**all方法示例：**

例：查询图书所有信息。

**filter方法示例：**

条件格式：

模型类属性名__条件名=值

查询图书评论量为34的图书的信息：

**a) 判等 条件名:exact**

```python
例：查询编号为1的图书。
# Django models 语法
BookInfo.objects.get(id=1)
```

**b) 模糊查询**

例：查询书名包含'传'的图书。contains

```python
BookInfo.objects.filter(btitle__contains='传')
```

例：查询书名以'部'结尾的图书 endswith 开头:startswith

```python
BookInfo.objects.filter(btitle__endswith='部')
```

**c)空查询 isnull**

```sql
例：查询书名不为空的图书。isnull
select * from booktest_bookinfo where btitle is not null;
```

```python
# Django models 语法
BookInfo.objects.filter(btitle__isnull=False)
```

**d)范围查询** in

```sql
例：查询id为1或3或5的图书。
select * from booktest_bookinfo where id in (1,3,5);
```

```python
# Django models 语法
BookInfo.objects.filter(id__in = [1,3,5])
```

**e)比较查询** gt(greate than) lt(less than) gte(equal) 大于等于

lte 小于等于

```sql
例：查询id大于3的图书。
Select * from booktest_bookinfo where id>3;
```

```python
# Django models 语法
BookInfo.objects.filter(id__gt=3)
```

**f)日期查询**

```python
# 例：查询1980年发表的图书。
BookInfo.objects.filter(bpub_date__year=1980)
```

```python
# 例：查询1980年1月1日后发表的图书。
from datetime import date
BookInfo.objects.filter(bpub_date__gt=date(1980,1,1))
```

**exclude方法示例：**

```python
# 例：查询id不为3的图书信息。
BookInfo.objects.exclude(id=3)
```

**order_by方法示例：**

**作用：**进行查询结果进行排序。

```python
# 例：查询所有图书的信息，按照id从小到大进行排序。
BookInfo.objects.all().order_by('id')
```

```python
# 例：查询所有图书的信息，按照id从大到小进行排序。
BookInfo.objects.all().order_by('-id')
```

```python
# 例：把id大于3的图书信息按阅读量从大到小排序显示。
BookInfo.objects.filter(id__gt=3).order_by('-bread')
```

## 6. **F对象**

作用：用于类属性之间的比较。

**使用之前需要先导入**：

```python
from django.db.models import F
```

```python
# 例：查询图书阅读量大于评论量图书信息。
BookInfo.objects.filter(bread__gt=F('bcomment'))
```

```python
# 例：查询图书阅读量大于2倍评论量图书信息。
BookInfo.objects.filter(bread__gt=F('bcomment')*2)

```

## 7. **Q对象**

作用：用于查询时条件之间的逻辑关系。not and or，可以对Q对象进行&|~操作。

**使用之前需要先导入：**

```python
from django.db.models import Q
```

```python
# 例：查询id大于3且阅读量大于30的图书的信息。
BookInfo.objects.filter(id__gt=3, bread__gt=30)
BookInfo.objects.filter(Q(id__gt=3)&Q(bread__gt=30))
```

```python
# 例：查询id大于3或者阅读量大于30的图书的信息。
BookInfo.objects.filter(Q(id__gt=3)|Q(bread__gt=30))
```

```python
# 例：查询id不等于3图书的信息。
BookInfo.objects.filter(~Q(id=3))
```

## 8. **聚合函数**

作用：对查询结果进行聚合操作。

sum count avg max min

**aggregate：调用这个函数来使用聚合。 返回值是一个字典**

**使用前需先导入聚合类：**

```python
from django.db.models import Sum,Count,Max,Min,Avg
```

```python
# 例：查询所有图书的数目。
BookInfo.objects.all().aggregate(Count('id'))

{'id__count': 5}
```

```python
# 例：查询所有图书阅读量的总和。
BookInfo.objects.aggregate(Sum('bread'))
{'bread__sum': 126}
```

**count函数 返回值是一个数字**

作用：统计满足条件数据的数目。

```python
# 例：统计所有图书的数目。
BookInfo.objects.all().count()
BookInfo.objects.count()
```

```python
# 例：统计id大于3的所有图书的数目。
BookInfo.objects.filter(id__gt=3).count()
```

小结:

![Django%20%E6%A8%A1%E5%9E%8B%203759c/image20.png](Django%20%E6%A8%A1%E5%9E%8B%203759c/image20.png)

**参考文档：**

[http://python.usyiyi.cn/translate/django_182/ref/models/querysets.html](http://python.usyiyi.cn/translate/django_182/ref/models/querysets.html)

## 9. **查询集**

all, filter, exclude, order_by调用这些函数会产生一个查询集，QuerySet类对象可以继续调用上面的所有函数。

### **9.1 查询集特性**

1. 惰性查询：只有在实际使用查询集中的数据的时候才会发生对数据库的真正查询。
2. 缓存：当使用的是同一个查询集时，第一次使用的时候会发生实际数据库的查询，然后把结果缓存起来，之后再使用这个查询集时，使用的是缓存中的结果。

### 9.2 限制查询集

可以对一个查询集进行取下标或者切片操作来限制查询集的结果。

对一个查询集进行切片操作**会产生一个新的查询集**，下标不允许为负数。

取出查询集第一条数据的两种方式:

[取出查询集](Django%20%E6%A8%A1%E5%9E%8B%203759c/%E5%8F%96%E5%87%BA%E6%9F%A5%E8%AF%A2%E9%9B%86%20f21dc.csv)

***exists:判断一个查询集中是否有数据。True False***

## 10. **模型类关系**

1. 一对多关系

> 例：图书类-英雄类
> 
> 
> models.ForeignKey() 定义在多的类中。
> 
1. 多对多关系

> 例：新闻类-新闻类型类 体育新闻 国际新闻
> 
> 
> models.ManyToManyField() 定义在哪个类中都可以。
> 
1. 一对一关系

> 例：员工基本信息类-员工详细信息类. 员工工号
> 
> 
> models.OneToOneField定义在哪个类中都可以。
> 

## 11. **关联查询（一对多）**

### 11.1 查询和对象关联的数据

在一对多关系中，一对应的类我们把它叫做一类，多对应的那个类我们把它叫做多类，我们把多类中定义的建立关联的类属性叫做关联属性。

```python
# 例：查询id为1的图书关联的英雄的信息。
b=BookInfo.objects.get(id=1)
b.heroinfo_set.all()
```

**通过模型类查询：**

```python
HeroInfo.objects.filter(hbook__id=1)
```

```python
# 例：查询id为1的英雄关联的图书信息。
h = HeroInfo.objects.get(id=1)
h.hbook
```

**通过模型类查询：**

```python
BookInfo.objects.filter(heroinfo__id=1)
```

**格式：**

![Django%20%E6%A8%A1%E5%9E%8B%203759c/image21.png](Django%20%E6%A8%A1%E5%9E%8B%203759c/image21.png)

由一类的对象查询多类的时候：

一类的对象.多类名小写_set.all() #查询所用数据

由多类的对象查询一类的时候：

多类的对象.关联属性 #查询多类的对象对应的一类的对象

由多类的对象查询一类对象的id时候：

多类的对象. 关联属性_id

### **11.2 通过模型类实现关联查询**

![Django%20%E6%A8%A1%E5%9E%8B%203759c/image22.png](Django%20%E6%A8%A1%E5%9E%8B%203759c/image22.png)

例：查询图书信息，要求图书关联的英雄的描述包含'八'。

```python
BookInfo.objects.filter(heroinfo__hcomment__contains='八')
```

例：查询图书信息，要求图书中的英雄的id大于3.

```python
BookInfo.objects.filter(heroinfo__id__gt=3)
```

例：查询书名为“天龙八部”的所有英雄。

```python
HeroInfo.objects.filter(hbook__btitle='天龙八部')
```

通过多类的条件查询一类的数据：

```python
一类名.objects.filter(多类名小写__多类属性名__条件名)
```

通过一类的条件查询多类的数据：

```python
多类名.objects.filter(关联属性__一类属性名__条件名)
```

## 12.**插入、更新和删除**

> 调用一个模型类对象的save方法的时候就可以实现对模型类对应数据表的插入和更新。
> 
> 
> 调用一个模型类对象的delete方法的时候就可以实现对模型类对应数据表数据的删除。
> 

## 13. **自关联**

![Untitled](Django%20%E6%A8%A1%E5%9E%8B%203759c/Untitled%201.png)

自关联是一种特殊的一对多的关系。

案例：显示广州市的上级地区和下级地区。

地区表：id, atitle, aParent_id;

mysql终端中批量执行sql语句：

```bash
source areas.sql;
```

## 14. **管理器**

BookInfo.objects.all()->objects是一个什么东西呢？

> 答：objects是Django帮我自动生成的管理器对象，通过这个管理器可以实现对数据的查询。objects是models.Manger类的一个对象。自定义管理器之后Django不再帮我们生成默认的objects管理器。
> 

### **14.1 自定义模型管理器类**

1. 自定义一个管理器类，这个类继承models.Manger类。
2. 再在具体的模型类里定义一个自定义管理器类的对象。

### 14.2 自定义管理器类的应用场景

1. 改变查询的结果集。
    
    比如调用BookInfo.books.all()返回的是没有删除的图书的数据。
    
2. 添加额外的方法。
    
    管理器类中定义一个方法帮我们操作模型类对应的数据表。
    
    使用self.model()就可以创建一个跟自定义管理器对应的模型类对象。
    
    **小结:**
    
    ![Django%20%E6%A8%A1%E5%9E%8B%203759c/image24.png](Django%20%E6%A8%A1%E5%9E%8B%203759c/image24.png)
    

## 15. **元选项**

Django默认生成的表名：

应用名小写_模型类名小写。

**元选项：**

需要在模型类中定义一个元类Meta,在里面定义一个类属性db_table就可以指定表名。

![Django%20%E6%A8%A1%E5%9E%8B%203759c/image25.png](Django%20%E6%A8%A1%E5%9E%8B%203759c/image25.png)