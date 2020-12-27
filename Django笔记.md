**设计模型**

Django无需数据库就可以使用，它提供了**对象关系映射器**，通过此技术，可以使用python代码来描述数据库结构，可以使用强大的**数据-模型语句**来描述数据模型。

**动态管理接口**

当模型完成定义，Django会自动生成一个专业的生产级**管理接口**---一个允许认证用户添加、更改和删除对象的web站点。
创建Django应用的典型流程是，先建立数据模型，然后搭建管理站点，之后客户或者员工就可以向网站里填充数据了。

**规划URLs**

为了设计自己的**URLconf**，需要创建一个叫做 URLconf 的 Python 模块。这是网站的目录，它包含了一张 URL 和 Python 回调函数之间的映射表。URLconf 也有利于将 Python 代码与 URL 进行解耦。一旦有 URL 路径匹配成功，Django 会调用相应的视图函数。每个视图函数会接受一个请求对象——包含请求元信息——以及在匹配式中获取的参数值。

**编写视图**

视图函数的执行结果只可能有两种：返回一个包含请求页面元素的**HttpResponse**对象，或者是抛出**Http404**这类异常。通常来说，一个视图的工作就是：从参数获取数据，装载一个模板，然后将根据获取的数据对模板进行渲染。

**设计模板**

Django 允许设置搜索模板路径，这样可以最小化模板之间的冗余。在 Django 设置中，可以通过 **DIRS** 参数指定一个路径列表用于检索模板。如果第一个路径中不包含任何模板，就继续检查第二个，以此类推。

注意，你并不是非得使用 Django 的模板系统，你可以使用其它你喜欢的模板系统。尽管 Django 的模板系统与其模型层能够集成得很好，但这不意味着你必须使用它。同样，你可以不使用 Django 的数据库 API。你可以用其他的数据库抽象层，像是直接读取 XML 文件，亦或直接读取磁盘文件，你可以使用任何方式。Django 的任何组成——模型、视图和模板——都是独立的。

**runserver会自动重新加载：** 用于开发的服务器在需要的情况下会对每一次的访问请求重新载入一遍 Python 代码。所以你不需要为了让修改的代码生效而频繁的重新启动服务器。然而，一些动作，比如添加新文件，将不会触发自动重新加载，这时你得自己手动重启服务器。

**创建一个应用的大致流程：**

```
django-admin startproject 名称       创建一个项目    
python manage.py runserver          启动django自带的用于开发的简易服务器
python manage.py startapp 名称       创建一个应用
在settings中注册应用
项目 VS 应用
项目和应用有什么区别？应用是一个专门做某件事的网络应用程序——比如博客系统，或者公共记录的数据库，或者小型的投票程序。
项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用。应用可以被很多个项目使用。

编写应用目录下的view.py文件
应用目录下创建一个urls.py文件
在urls.py中进行路径映射
在项目urls.py文件中插入一个include()，将应用的路径包含进去
```

**函数path()参数释义**
共有四个参数，两个必须参数：route和view，两个可选参数：kwargs和name。
**route：** 一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 urlpatterns 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。
这些准则不会匹配 GET 和 POST 参数或域名。例如，URLconf 在处理请求 https://www.example.com/myapp/ 时，它会尝试匹配 myapp/ 。处理请求 https://www.example.com/myapp/?page=3 时，也只会尝试匹配 myapp/。
**view：** 当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 HttpRequest 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。
**kwargs：** 任意个关键字参数可以作为一个字典传递给目标视图函数。
**name：** 为你的 URL 取名能使你在 Django 的任意地方唯一地引用它，尤其是在模板中。这个有用的特性允许你只改一个文件就能全局地修改某个 URL 模式。

**创建模型**
在 Django 里写一个数据库驱动的 Web 应用的第一步是定义模型 - 也就是数据库结构设计和附加的其它元数据。
数据库的表名在models.py文件体现为类名，字段体现为类变量。

**激活模型**
在项目settings.py的INSTALLED_APPS添加应用的config类，这个类在应用目录下的app.py中。
添加后运行python manage.py makemigrations 应用名。

**改变模型需要的步骤：**

- 编辑models.py文件，改变模型
- 运行python manage.py makemigration 为模型的改变生成迁移文件
- 运行python manage.py migrate 来应用数据库迁移

**模板命名空间**
虽然可以将模板文件直接放在 polls/templates 文件夹中（而不是再建立一个 polls 子文件夹），但是这样做不太好。Django 将会选择第一个匹配的模板文件，如果有一个模板文件正好和另一个应用中的某个模板文件重名，Django 没有办法 *区分* 它们。我们需要帮助 Django 选择正确的模板，最好的方法就是把他们放入各自的 *命名空间* 中，也就是把这些模板放入一个和 *自身* 应用重名的子文件夹里。

**render函数**
「载入模板，填充上下文，再返回由它生成的 HttpResponse 对象」是一个非常常用的操作流程。render函数封装了这个操作。

```
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))

等价于==>
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

**get_object_or_404函数**
尝试用 get() 函数获取一个对象，如果不存在就抛出 Http404 错误也是一个普遍的流程。get_object_or_404函数提供了这个功能。

**无论何时，当需要创建一个改变服务器端数据的表单时，请使用 method="post" 。**

**GET & POST**
request.POST 是一个类字典对象，让你可以通过关键字的名字获取提交的数据。注意，Django 还以同样的方式提供 request.GET 用于访问 GET 数据 —— 但我们在代码中显式地使用 request.POST ，以保证数据只能通过 POST 调用改动。

**通用视图**
DetailView 期望从 URL 中捕获名为 "pk" 的主键值。
默认情况下，通用视图 DetailView 使用一个叫做 ```<app name>/<model name>_detail.html``` 的模板，类似地，ListView 使用一个叫做 ```<app name>/<model name>_list.html``` 的默认模板，template_name 属性是用来告诉 Django 使用一个指定的模板名字，而不是自动生成的默认名字。 
对于 DetailView ， question 变量会自动提供—— 因为我们使用 Django 的模型 (Question)， Django 能够为 context 变量决定一个合适的名字。然而对于 ListView， 自动生成的 context 变量是 question_list。为了覆盖这个行为，我们提供 context_object_name 属性，表示我们想使用 latest_question_list。

**静态文件查找**
Django 的 STATICFILES_FINDERS 设置包含了一系列的查找器，它们知道去哪里找到 static 文件。AppDirectoriesFinder 是默认查找器中的一个，它会在每个 INSTALLED_APPS 中指定的应用的子文件中寻找名称为 static 的特定文件夹，就像我们在 polls 中刚创建的那个一样。管理后台采用相同的目录结构管理它的静态文件。

---

## 视频教程

**MVC框架**
model(模型，操作数据库) view(视图，页面显示) controller(控制，业务逻辑)
![需要联网](http://m.qpic.cn/psc?/V51KKpT44VSuUp4Duy8J3cJxZq01lTay/ruAMsa53pVQWN7FLK88i5n8IN68KPoWP9wy6FMFUTCrBHRyih34i2Dc.s68cv4yWoXy990wJBQAPNa1NHWnnJr.UeVbYrn4HMkjOGTgoVtk!/b&bo=KQKlAgAAAAABB6w!&rf=viewer_4)
Django中的MTV：
Model Template(相当于MVC中的V) View(相当于MVC中的C)

**Django处理浏览器请求的流程：**
1. 请求发送到wsgi，wsgi封装请求的相关数据（request）
2. Django去匹配路径，根据路径判断要执行哪个函数
3. 执行函数，函数中处理具体的业务逻辑
4. 函数返回响应，Django按照HTTP协议响应的格式进行返回

**发请求的途径：**
- 在浏览器的地址栏中输入地址 回车 --》 get
- a标签 --》 get
- form表单 post/get

**get和post请求的区别：**
- get 获取资源
?k1=v1&k2=v2 &emsp;request.GET拿到内容，但是不是意味着就是get请求提交的数据，如果post请求也携带有数据，也是可以通过这种方式获取数据的。
get请求没有请求体
- post 提交数据
request.POST
数据在请求体中

### settings配置：
- BASE_DIR，项目的根目录
- DEBUG，网页出错是否提示具体信息
- STATIC_URL，可以修改，不是存放静态文件的文件夹名，相应的，引用静态文件时也是用这个地址，而不是存放的文件夹名。
- STATICFILES_DIRS，拼接存放静态文件的文件夹和根目录，有顺序 
- INSTALLED_APPS，注册APP，方式有两种，一是直接写app名称，二是app名称.apps.APP名称config。
- MIDDLEWARE，中间件
    &emsp;注释掉csrf的中间件，可以提交POST请求。这个可以通过在表单中加上csrf_token解决，也可以在类方法上加一个@csrf_exempt（from django.views.decorators.csrf import csrf_exempt）的装饰器解决，这样可以避免注释掉csrf中间件。
- TEMPLATES，模板
    &emsp;DIRS:[os.path.join(BASE_DIR,'templates')]
- DATABASE，数据库
- DATETIME_FORMAT = 'Y-m-d H:i:s'同时USE_L10N=False就可以自动将时间按指定格式显示，只显示时间：TIME_FORMAT='H:i:s'，只显示日期：DATE_FORMAT='Y-m-d'。


### urls配置：
路由设置
from django.conf.urls import url
urlpatterns，url(地址， 函数)

**函数返回的值：**
- HttpResponse('字符串') 返回字符串
- render()  &emsp;&emsp;返回一个HTML页面
- redirect('地址')  &emsp;&emsp;重定向


**render：参数释义**
request，模板名称，字典（上下文）...


调试时加上浏览器中的禁用缓存，谷歌浏览器在检查 - > network 里面。

**form表单注意的点：**
- form标签的属性action指定提交的地址（不写默认当前地址），method请求方式（默认get）
- input标签要有name属性，有的标签还要有value
- 有一个button按钮或者是type="submit"的input

要提交POST请求的必要操作：在settings.py中注释一个中间件（MIDDLEWARE）---csrf  &emsp; &emsp;目前

一些基础操作：
- request.method      &emsp;&emsp;请求方式 GET POST
- request.POST    &emsp;&emsp;POST请求提交的数据
- request.POST.get('k1')      &emsp;&emsp;POST请求提交的数据中k1对应的值，如果对应的数据是个列表则只能取到列表的最后一个值
- request.POST.getlist('k1')    &emsp;&emsp;POST请求提交的数据中k1对应的值，此时值是列表
- request.GET     &emsp;&emsp;url携带的参数 ?k1=v1&k2=v2
- request.GET.get('k1') &emsp;&emsp;获取url携带参数中k1对应的值
- return HttpResponse('字符串')  &emsp; &emsp;返回字符串
- return render(request, '模板的文件名')     &emsp; &emsp;返回HTML页面
- return redirect('地址')    &emsp;&emsp;重定向

### ORM 对象关系映射
<font color=red>QuerySet是惰性的，使用filter进行查询时，并没有真正从数据库中获得数据，只有对QuerySet进行操作时才真正从数据库获取数据。</font>
<font color=blue>查询优化可以考虑建立索引。索引分为单字段索引和组合索引
**单字段索引**：barcode = models.CharField(verbose_name='货架码', max_length=12, db_index=True)，db_index设置为True即为该字段创建索引。
**组合索引**：
class Meta:
&emsp;&emsp;index_together = ["store", "sensor"]</font>
<font color=orange>使用(objects)QuerySet.values(values_list)进行按需取数据，不需要一次取出所有的字段数据。
**values(*fields)**：返回一个ValueQuerySet(QuerySet的一个子类)，迭代时返回字典，用法示意：QuerySet.values('name', 'time')
**values_list(*fields, flat=False)**:与values类似，只是在迭代时返回的是元组而不是字典，如果只传递一个字段，可以传递flat参数，当flat为True时，返回的结果为单个值而不是元组，此时可以强转为list得到原生list而不是对象列表。</font>
通过操作类、对象的方式去操作数据库
对应关系：
类 —— 》 表
对象 —— 》 数据行（记录）
属性 —— 》 字段

**操作已经存在的数据库**
- 配置settings.py文件
- 导入pymysql  pymysql.install_as_MySQLdb()
- python manage.py inspectdb 根据数据库自动生成新的models文件
- python manage.py inspectdb > models.py 导入到文件，此时manage.py同级目录下生成一个models.py文件
- 使用models.py文件覆盖app中的models文件
- 修改models文件中meta class的managed = True
- 使models.py和数据库进行同步，python manage.py migrate

**若出现表已存在的错误，可以到makemigrations生成的文件中去删掉对应的表。或者使用python manage.py migrate --fake-initial。如果是因为有外键存在，需要初始化多个表，且有部分数据表已创建，又有部分未创建，可以使用--fake <appname>来处理。**

使用ORM
1. 在settings中配置数据库的连接
2. 在app下的models中写类
3. 执行数据库迁移的命令
```
python manage.py makemigrations
python manage.py migrate
```

**操作：**
from app名称 import models
- 查：
models.类名.objects.all()：获取表中所有的数据  QuerySet  对象列表
models.类名.objects.get(pk=pk)：获取一条数据，不存在会报错，获取多条数据报错
models.类名.objects.filter(pk=pk)：获取满足条件的对象    对象列表
.order_by('字段')：排序  默认是正向排序，加上负号为逆向排序。
```
查询条件
    __gt    大于
    __gte   大于等于
    __lt    小于
    __lte   小于等于
    __in    在...中     User.objects.filter(age__in=[10, 20, 30])，只返回年龄等于10、20、30的记录
    __range 在..范围中，闭区间  User.objects.filter(created_time__range= ('2018-05-01','2018-06-01'))
                                返回创建时间在这个范围的用户，这条语句多用于时间范围，需要时查文档。
    like
        __exact     精确等于 like 'aa'
        __iexact    精确等于 忽略大小写 ilike 'aa'
        __contains  包含 like '%aa%'
        __icontains 包含 忽略大小写 ilike '%aa%'
    __isnull    判空
    不等于，不包含于      User.objects.filter().exclude(age=10) 查询年龄不为10的用户
                         User.objects.filter().exclude(age__in=[10, 20])    查询年龄不等于10、20的用户
    __startswith         以什么开头
    __istartswith        以什么开头，忽略大小写
    __endswith           以什么结尾
    __iendswith          以什么结尾，忽略大小写

若单条语句不足以满足查询逻辑，可以进行级联：
    可以直接在filter中写多个条件。
    news = models.News.objects.order_by('time').filter(time__gte=start)
    news = news.filter(time__lte=end)
或者：
    news = models.News.objects.order_by('time').filter(time__gte=start).filter(time__lte=end)
```
- 增
models.类名.objects.create(字段='xxx')
表带有外键时，create中可以用外键名称=对应的对象进行新增，也可以使用外键名称_id=对应对象的id进行新增。
表中有多对多关系时，可以用create创建的对象.多对多字段名.set([])即可新增或者修改对应的多对多关系。
- 删
models.类名.objects.get(字段='').delete() &emsp;单个对象删除
models.类名.objects.filter(字段='').delete() &emsp;多个对象删除
- 改
x = models.类名.objects.get(字段='')
x.字段 = 'xxx'
x.save() &emsp;提交到数据库
models.类名.objects.filter(pk='xx').update(字段='xx')  直接更新，批量更新，对比上面的方法还有一个优势：转换成SQL语句时只转换update填的字段，上述方法会将不需要更改的字段也转成SQL语句。
对象.多对多字段名.set(列表)   &emsp;&emsp;设置多对多关系


**设置数据库：**
1. 创建MySQL数据库
1. 配置settings：
    - ENGINE修改为MySQL，修改NAME的路径为MySQL数据库名称
    - HOST：IP地址
    - PORT：3306  MySQL默认的数据库
    - USER：
    - PASSWORD：
2. 使用pymysql连接数据库，习惯写入到与项目同名的文件夹下的```__init__.py```
```
import pymysql
pymysql.install_as_MySQLdb()
```

**models模板中可以使用的类别：**
```
CharField(max_length=32)    varchar(32)
ForeignKey()    外键（一对多），一个参数，关联的表名，可以是字符串形式（没有先后顺序）也可以是变量形式，默认级联删除，on_delete指定，2.0版本后必填
                on_delete=models.CASCADE    级联删除
                            models.PROTECT    保护，还有关联的情况下，那个一不让删除
                            SET(v)  删除后设置为某个值
                            SETDEFAULT  删除后设置为默认值，需要添加一个default=参数
                            SET_NULL    删除后设置为NULL
                            DO_NOTHING  什么都不做
    带有外键的表，orm创建时会自动加一个'字段名_id'的字段，可以通过该字段查询关联外键的id。在数据库中只显示'字段名_id'的字段，不会直接显示所定义的字段名。使用对象.外键名可以得到外键对应的对象。
ManyToManyField()   多对多关系，参数是需要关联的表，表名或者表名字符串，不会新增字段会自动创建第三张表。
    使用对象.名称取得一个关系管理对象，使用对象.名称.all()可以取得所关联的所有对象
AutoField()     自增字段，参数指定primary_key=True可以设置成主键，自增字段只有一个
IntegerField()  整数类型，数值范围是-21亿到21亿，十位数字。
DateField()     年月日
DateTimeField() 年月日-时分秒，存储时按UTC时间存储，展示时会自动转换。
                auto_now=True       修改和保存数据时自动保存
                auto_now_add=True   仅保存数据时自动保存时间


字段参数：
    null=True       该字段在数据库中允许为空
    blank=True      表单填写可以为空
    db_column       数据库字段的列名
    default         默认值
    primary_key     创主键
    db_index        建索引
    unique          唯一值
    verbose_name    显示字段名
    choices         用户选择的参数

使用Django的admin：
    1. 创建一个超级用户
        python manage.py createsuperuser
    2.在APP下的admin.py中注册model
        from django.contrib import admin
        from app01 import models

        admin.site.register(models.表名)
    3.登录


Meta的参数：
    db_table            表名
    index_together      联合索引
    unique_together     联合唯一索引

```

**外键的操作**
```
基于对象的查询
    正向查询
        book_obj = models.Book.objects.get(pk=1)
        book_obj.pub    book_obj.pub_id
    反向查询
        pub_obj = models.Publisher.objects.get(pk=1)
        在外键定义时没有指定related_name时
            pub_obj.book_set    类名小写_set->获取关系管理对象
            pub_obj.book_set.all()  获取所有关联的对象，queryset
        指定related_name='books'
            pub_obj.books.all()
基于字段的查询
    models.Book.objects.filter(pub__name='123')     通过__跨到关联的表进行筛选，pub是外键字段名
    不指定related_name='books'
        models.Publisher.objects.filter(book__name='123')   通过__跨到关联的表进行查询，book是类名
    指定related_name='books'
        models.Publisher.objects.filter(books__name='123')
        指定related_query_name='abc'
            models.Publisher.objects.filter(abc__name='123')
下述 多对多的操作 中的set add create方法可以应用于 外键的操作，当外键的定义中声明了null=True时可以使用clear remove。需要注意的是，set的参数列表只能是对象列表，不能为id列表，add create的参数只能是对象。
```

**多对多的操作**
```
基于对象的查询和基于字段的查询和 外键的操作 一致。
设置多对多关系
    obj.字段名.set([])      参数可以是id列表也可以是对象列表
添加多对多关系
    obj.字段名.add(1, 2, ...)
删除多对多关系
    obj.字段名.remove(1, 2, ...)
清空多对多关系
    obj.字段名.clear()
新建一个关联的对象，并建立关联关系
    obj.字段名.create(关联表对应的字段)

```
**聚合和分组**
```
from django.db.models import Max, Min, Count, Sum, Avg
聚合aggregate 终止子句
models.Book.objects.all().aggregate(Max("price"), Min("price"))，返回一个字典，可以指定max=Max("price")来修改返回字典的键名。
分组    加上额外的信息，没有修改到表
models.Book.objects.annotate(Count("authors")) 可以用values()方法返回需要的字段
models.Book.objects.values("author", "author__name").annotate(sum=Sum("price")) 分组使用values指定分组的字段
```
**F查询和Q查询**
```
from django.db.models import F,Q
F查询：查询时，条件是比较两个字段。
models.Book.objects.filter(sale_gt=F('id'))     查询sale大于id的记录
models.Book.objects.filter(id__lte=3).update(sale=F('sale)*2+13)    将sale改为原来的2倍加上13
Q查询：构建查询对象，方便构建查询时的逻辑。
    |   或
    &   与
    ~   非
models.Book.objects.filter(Q(id__lt=3)|Q(id__gt=5))     查询id大于3或者小于5的记录
```
**事物**
```
from django.db import transaction
with transaction.atomic():
要进行异常处理时，将try卸载with transaction.atomic()的外面。
```

**以脚本文件进行orm操作**

```
在项目同目录想建一个python脚本
import os
从manage.py中将os.environ.setdefault()复制过来
import django
django.setup()
from app import models


数据库操作：
    all()
    get()
    filter()
    exclude()       获取不满足条件的数据
    order_by()      默认升序，加-改为降序，支持多个字段排序
    reverse()       对已排序的对象列表进行翻转
    values()        字典类型的queryset，可以指定字段从而只取出对应字段的值
    values_list()   元祖类型的queryset，不指定字段时，返回所有字段的值，
                    可以指定字段，取出对应字段的值组成元祖，只指定一个字段时，
                    可以使用flat=True将结果转换成list
    distinct()      数据完全一样时去重，没有参数
    count()         计数，效率比len高
    first()         获取第一个元素
    last()          获取最后一个元素
    exists()        判断是否有结果
```


### Django模板语法

**过滤器**
语法：
{{变量名 | 过滤器}}
{{变量名 | 过滤器：‘参数’ }} ：后面没有空格，否则会报错，可以连续使用
**过滤器**
- default 变量为空或者未传递值时显示参数
- filesizeformat 将数字转换成人类可读的文件尺寸
- add 加减法，字符串拼接
- lower 小写
- upper 大写
- title 首字母大写
- length 变量的长度
- slice 切片，和python的切片是一样的，参数是字符串形式
- first 第一个
- last 最后一个
- join 拼接
- truncatechars 按字符截断
- truncatewords 按单词截断
- date 日期格式化 'Y-m-d H:i:s'
- safe Django会自动对HTM和JS语法进行转义，变成字符串，为了安全，此管道符告诉Django不用转义。另一种方式，加一个mark_safe函数。
- widthratio a b c 结果为a/b*c，结果取整，不保留小数

**自定义方法：**
1. 在app下创建一个名为templatetags的python包
2. 创建一个python文件，文件名自定义
3. 在python包中写：
from django import template
register = template.Library()
4. 写函数+装饰器
```
filter
    @register.filter
    def  xxx(变量，参数):   只能有两个参数
        .....
        return 'xxxx'

simple_tag
    @register.simple_tag
    def xxx(a, b, c, d ...)   参数个数不受限制
        ......
        return 'xxxxx'

inclusion_tag
    @register.inclusion_tag('xxx.html')
    def xxx(a, b, c ...):   参数个数不受限制，需要一个HTML模板  实际返回的是渲染后的模板文本
        ......
        return {'k1':v1 ...}
```
5. 在模板中导入包{%load 文件名%}
```
{% load 文件名%}
{{ yyy | xxx:'yyyy' }}  filtere
{% xxx a b c ... %} simple_tag
{% xxx a b c ... %} inclusion_tag，实际作用是在调用位置添加一小段渲染的HTML模板
```

**语法**

```
模板中调用方法不需要加小括号，没有这种写法。需要取列表、字典等中的某个值，可以用.0、.key等，但是列表不支持负向索引。
当模板系统遇到.时会按如下顺序查找：
1. 字典的key
2. 属性和方法
3. 索引
render(request, 模板名, 参数（字典）)函数传递参数
{{ 变量名 }}    获取传递的变量值
{% 标签 %}
{% for i in 变量名 %}
    {{forloop.counter}}     获取循环次数，.counter0表示从0开始，.revcounter倒序到1结束，.revcounter0倒序到0结束。
    forloop.first， forloop.last，布尔值，是否是第一次/最后一次。
    forloop.parentloop，当前循环的外层循环
    {% empty %}，循环不能显示则显示empty中的内容，作为一个提示信息
    {% if %}    不支持算术运算，不支持连续判断(语法上支持，是和python的语法不一样)
        xxx
    {% else %}
        yyy
    {% endif %}
    with，给变量重新取个名字
    csrf_token 写在form表单中，允许页面提交信息，可以不用注释csrf中间件
    循环体
{% endfor %}
```

**母版和继承**
母版：
1. 一个包含多个页面的公共部分
2. 定义多个block块，让子页面进行覆盖

继承：
1. {% extends '母版的名字' %}
2. 重新复写block块

注意点：
1. {% extends '母版的名字' %} 母版的名字带引号
2. {% extends '母版的名字' %} 写在第一行，上面不再写内容，上面的内容会显示，下面的不会显示
3. 多写一个css、js的block块，让不同的子页面有不同的样式

**组件**
1. 把一小段想要公用的HTML文本写入一个HTML文件
2. 在需要该组件的模板中``` {% include 'HTML文件名' %}```

**静态文件相关**
```
{% load static %}
<link rel="stylesheet" href="{% static 'css/dsb.css' %}">
做一个路径拼接，当改动settings中的STATIC_URL时可以避免修改静态文件的引用
```

### Django的View

**CBV和FBV**
FBV：function based view
CBV：class based view
```
from django.views import View

class Xxx(View):

    def get(self, request)
        #专门处理get请求
        return response
    
    def post(self, request)
        #专门处理post请求
        return response


urls中：
url(r'^xx/', Xxx.as_view())
```

**as_view()的流程**
1. 项目运行时加载urls.py文件，执行类.as_view()方法
2. as_view()执行后，内部定义了一个view函数，并且返回
3. 请求到来的时候，执行view函数：
    1. 实例化类 ———》 self
    2. self.request = request
    3. 执行self.dispatch(request, *args, **kwargs)的方法
        1. 判断请求方式是否被允许
            1. 允许：
            通过反射获取请求方式对应的请求方法 ——》 handler
            获取不到self.http_method_not_allowed ——》 handler
            2. 不允许：
            self.http_method_not_allowed ——》 handler
        2. 执行handler，返回结果


**CBV加装饰器**
``` 
from django.utils.decorators import method_decorator 
```
1. 加在方法上
```
@method_decorator(自定义的装饰器)
def get(self, request):
```

2. 加在dispatch上，需要自己重写一遍dispatch方法，相当于所有的类方法都加了装饰器，因为都需要执行dispatch方法
```
@method_decorator(自定义的装饰器)
def dispatch(self, request, *args, **kwargs):
    #加一些所有类方法都需要的操作
    ret = super().dispatch(request, *args, **kwargs)
    return ret

@method_decorator(自定义的装饰器,name='dispatch')
class PublisherAdd(View):
```

3. 加在类上
```
@method_decorator(自定义的装饰器,name='post')
...可以写多个
class PublisherAdd(View):
```

**request**
属性：
- request.method 请求方法 GET/POST
- request.GET URL上携带的参数 ?k1=v1&k2=v2
- request.POST post提交的请求信息 {} 编码方式是URLencode时才有数据
- request.path_info 路径信息，不包含IP和端口，也不包含参数
- request.body 请求体，bytes类型，无论什么编码方式都有数据
- request.session session
- request.COOKIES cookie
- request.FILES 上传的文件，上传文件时需要指定编码方式。
- request.META 头的信息，一个标准的python字典，包含所有的HTTP头部信息。

方法：
- request.get_full_path() 完整的路径信息，不包含IP和端口，包含参数
- request.is_ajax() 请求是否是ajax请求

### 路由
从上往下匹配，可能出现截胡的情况，最好加上$。
```
from django.conf.urls import url

urlpatterns = [
    url(正则表达式， views视图， 参数， 别名)，
]
```

**正则表达式**
```
^   以什么开头
$   以什么结尾
\d  数字
\w  字符
{}  重复几次
[]  里面的一个
+   一个或多个
?   0个或一个
*   0个或多个
.   匹配换行符之外的字符
```

**分组**
分组和命名分组不可混用。
```
url(r'^blog/([0-9]{4})/\d{2}/$', views.blogs)
url地址上捕获参数，按照位置进行传参，传递给视图函数，类型为字符串。
```

**命名分组**
```
url(r'^blog/(?P<year>[0-9]{4})/(?P<month>\d{2}/$'), views.blogs),

url地址上捕获的参数会按照 关键字传参 方式传递给视图函数
```

**路由分发**
将应用的路由写在应用的文件夹中，在应用的文件中新建一个urls.py文件，按照项目路由格式进行添加。然后在项目路由中使用```url(r'前缀', include('app01.urls'))```。include从url同位置导入。

**url的命名和反向解析**
redirect里面写有reverse，可以直接redirect（命名）。
有修改url需求时，使用命名和反向解析可以直接修改url文件中的路径，而不需要在使用路径的python文件和模板中去逐一修改。
1. 静态路由
命名：```url(r'^blog123/$', views.blog, name='blog'),   # blog --> blog```
反向解析：
    - 模板：```{% url 'blog' %}     --> blog123```
    - py: ```reverse('blog')        ---> blog123```，通过reverse生成名称对应的路径，reverse从shotcut中导入。

2. 分组
命名和静态路由一样。
反向解析：
    - 模板：```{% url 'blogs' '2020' '02'%}  --> /blog123/2020/02/，后面两个为路由中的参数，根据自己的路由来定```
    - py：```reverse('blogs', args=('2018', '08'))      --> /blog123/2018/08/```

3. 命名分组
命名和静态路由一样，分组的用法也可以用，多出一种用法。
反向解析：
    - 模板：```{% url 'blogs' month='02' year='2018' %}     --> /blog123/2018/08/```
    - py：```reverse('blogs', kwargs=('year':'2018', 'month':'12'))     --> /blog123/2018/12/```






## 前后端分离
为前端提供API，返回json,xml格式数据
Django有CBV(class base vie)和FBV(function base view)

Web API接口和一般的url链接存在区别，**Web API接口概括：**
- url：长得像返回数据的url链接
    - https://api.map.baidu.com/place/v2/search
- 请求方式：get、post、put、patch、delete
    - 采用get方式请求上方接口
- 请求参数：json或xml格式的key-value类型数据
    - ak:6E823f587c95f0148c19993539b99295
    - region：上海
    - query：肯德基
    - output：json
- 响应结果：json或xml格式的数据
    - 上方请求参数的output参数值决定了响应数据的格式

**序列化**
api接口开发，最核心最常见的一个过程就是序列化，所谓序列化就是**把数据转换格式**，序列化可以分两个阶段：
1. 序列化：把我们识别的数据转换成指定的格式提供给别人。
2. 反序列化：把别人提供的数据转换/还原成我们需要的格式。

**restful规范**
1. 数据的安全保障：url链接一般都采用https协议进行传输 注：采用https协议，可以提高数据交互过程中的安全性
2. 接口特征表现，一看就知道是个api接口
    - 用api关键字标识接口url：
        - [https://api.baidu.com](https://api.baidu.com/)
        - https://www.baidu.com/api
    - 路飞的接口：https://api.luffycity.com/api/v1/course/free/
3. 多数据版本共存
    - 在url链接中标识数据版本
    - https://api.baidu.com/v1
    - https://api.baidu.com/v2
4. 数据即是资源，均使用名词（可复数）
    - 接口一般都是完成前后台数据的交互，交互的数据我们称之为**资源**
      - https://api.baidu.com/users
      - https://api.baidu.com/books
      - https://api.baidu.com/book
      - 一般提倡用资源的复数形式，在url链接中尽量不要出现操作资源的动词
    - 特殊的接口可以出现动词，因为这些接口一般没有一个明确的资源，或是动词就是接口的核心含义
      - https://api.baidu.com/place/search
      - https://api.baidu.com/login
5. 资源操作由请求方式决定（method）
    - 操作资源一般都会涉及到增删改查，我们提供请求方式来标识增删改查动作
      - https://api.baidu.com/books - get请求：获取所有书
      - https://api.baidu.com/books/1 - get请求：获取主键为1的书
      - https://api.baidu.com/books - post请求：新增一本书书
      - https://api.baidu.com/books/1 - put请求：整体修改主键为1的书
      - https://api.baidu.com/books/1 - patch请求：局部修改主键为1的书
      - https://api.baidu.com/books/1 - delete请求：删除主键为1的书
6. 过滤，通过在url上传参的形式传递搜索条件
    - https://api.example.com/v1/zoos?limit=10：指定返回记录的数量
    - https://api.example.com/v1/zoos?offset=10：指定返回记录的开始位置
    - https://api.example.com/v1/zoos?page=2&per_page=100：指定第几页，以及每页的记录数
    - https://api.example.com/v1/zoos?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序
    - https://api.example.com/v1/zoos?animal_type_id=1：指定筛选条件
7. 响应状态码
   7.1 正常响应
    - 响应状态码2xx
      - 200：常规请求
      - 201：创建成功

   7.2 重定向响应
    - 响应状态码3xx
      - 301：永久重定向
      - 302：暂时重定向

   7.3 客户端异常
    - 响应状态码4xx
      - 403：请求无权限
      - 404：请求路径不存在
      - 405：请求方法不存在

	7.4 服务器异常
    - 响应状态码5xx
      - 500：服务器异常
 8. 错误处理，应返回错误信息，error当做key
    {
        error: "无权限操作"
    }
    
 9. 返回结果，针对不同操作，服务器向用户返回的结果应该符合以下规范
    GET /collection：返回资源对象的列表（数组）
    GET /collection/resource：返回单个资源对象
    POST /collection：返回新生成的资源对象
    PUT /collection/resource：返回完整的资源对象
    PATCH /collection/resource：返回完整的资源对象
    DELETE /collection/resource：返回一个空文档
    
 10. 需要url请求的资源需要访问资源的请求链接
<!--  Hypermedia API，RESTful API最好做到Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么 -->
        {
            "status": 0,
            "msg": "ok",
            "results":[
                {
                    "name":"肯德基(罗餐厅)",
                    "img": "https://image.baidu.com/kfc/001.png"
                }
                ...
                ]
        }

**django rest_franmework(drf)**
核心思想：缩减编写api接口的代码
Django REST framework是一个建立在Django基础之上的Web 应用开发框架，可以快速的开发REST API接口应用。
**特点：**
- 提供了定义序列化器Serializer的方法，可以快速根据 Django ORM 或者其它库自动序列化/反序列化；
- 提供了丰富的类视图、Mixin扩展类，简化视图的编写；
- 丰富的定制层级：函数视图、类视图、视图集合到自动生成 API，满足各种需要；
- 多种身份认证和权限认证方式的支持；[jwt]
- 内置了限流系统；
- 直观的 API web 界面；
- 可扩展性，插件丰富

## 视频教程

**步骤：**
- 将请求的数据（如JSON格式）转换为模型类对象
- 操作数据库
- 将模型类对象转换为响应的数据（如JSON格式）

**序列化方式：**
数据表必须得有主键，否则报错。
**1和2使用HttpResponse返回，3和4使用DRF的Response返回**
1. 强转
    list(QuerySet.values('name', 'id'))，然后再使用json.dumps转换
2. from django.forms.models import model_to_dict
    遍历QuerySet，对每个Query使用model_to_dict然后添加到字典中，然后再使用json.dumps转换
3. from django.core import serializers
    serializers.serialize('json', QuerySet)，直接就成了json格式
4. from django.http.response import JsonResponse
    直接使用JsonResponse(字典)，即可返回json数据，此时的contentType为json。默认传递的数据是字典类型，要传递非字典数据，加上safe=False参数即可。
4. from rest_framework import serializers  建立一个继承serializers.Serializer的序列化类
    使用建立的类序列化QuerySet或者Query，使用.data属性即可返回。**注意：** 对于QuerySet需要加上many=True参数。
5. 在4的方法上继承serializers.ModelSerializer，然后定义一个内部类Meta，指定model=表名和fields="\_\_all\_\_"，这样即可进行序列化（可以写成一个字符串列表来定义需要序列化的字段，使用exclude指定需要排除的字段，如('id',)，使用depth指定嵌套深度（对外建有效）），有需要自定义的字段可以加进去，会覆盖。

**要想序列化关联表的多个字段，可以使用嵌套序列化，若是需要嵌套序列化可以接受列表则需要将many=True标志传递给嵌套的序列化器。**
**一对多外键字段的序列化：可以在定义字段序列化后修改__str__(self)方法，但是这样是直接写死了的，可以加上一个如source="publish.name"参数，即可返回需要序列化的字段。**
**多对多字段的序列化：可以用source="authors.all"参数，但是返回的是一个QuerySet，不方便观察，可以用serializers.SerializerMethodField()进行序列化，在下面加一个get_字段名(self, obj)的方法，其返回值即为序列化返回的序列化内容，用法示例如下**
```
def get_authors(self, obj):
    temp = []
    for author in obj.author.all():
        temp.append(author.name)
    return temp
```

**视图部分**
一张表需要构建两个类，一个类包含GET、POST方法，对表中的所有记录进行操作，另一个细节类包含GET、PUT、DELETE方法，操作具体的某条记录，需要传入id参数。

**from rest_framework.response import Response**
使用Response封装返回值可以使返回结果有组织结构。

**继承serializers.Serializer的序列化类的写法：**
字段和models中一样，将models替换为serializers，然后一对多或者多对多可以在方法中加上source="models类.键"参数，在多对多的表中返回一个Set，不是需要的，键名=serializers.SerializerMethodField()专门针对多对多，然后定义一个方法（get_键名(self, obj)），在方法中用一个循环即可取得值。

序列化类可以反序列化前端提交的数据，用法为BookSerializer(request.data)，得到的类有.is_valid()、.save()方法。反序列化更新（继承了ModelSerializer的序列化类自动实现update和create方法）时需要加一个data=request.data参数，用法例如：
```
book = Book.objects.filter(pk=id).first() 
bs = BookModelSerializers(,data=request.data)
if bs.is_valid():
    bs.save()
    return Response(bs.data)
else:
    return Response(bs.errors)
```

使用序列化类进行序列化时可以加上超链接，当表有关联表时。

**request：**
Django原生的：
request.POST：只有当contentType为urlencoded才会有返回值。
request.body：返回socket传递信息中的主体，无论contentType为何种类型都有值，类型为字符串。
restframework下的：
request.data：post请求
request.GET：GET请求

**restframework中的视图**
1. from rest_framework import mixins
from rest_framework import generics
两个类必须定义queryset和serializer_class
基础类继承mixins.ListModelMixin, mixins.CreateModelMixin, generics.GenericAPIView
get------调用list方法
post------调用create方法
细节类继承mixins.RetrieveModelMixin, mixins.DestroyModelMixin, mixins.UpdateModelMixin, generics.GenericAPIView
get------调用retrieve方法
delete------调用destroy方法
put------调用update方法

2. 使用通用的基于类的视图
两个类必须定义queryset和serializer_class
基础类继承generic.ListCreateAPIView
只需要定义
细节类继承generics.RetrieveUpdateDestroy

3. viewsets.ModelViewSet
from rest_framework import viewsets
一个类继承viewsets.ModelViewSet
定义queryset和serializer_class
此时，url中的as_view需要参数，字典，例如{'get':'list'}

**url**
from rest_framework import routers
rout = routers.DefaultRouter()
rout.register('地址前缀', 视图类)
然后在urlpatterns中加上url(r'^', include(rout.urls))，include需要从django.conf.urls中导入。

视图部分看完
跳看了路由控制