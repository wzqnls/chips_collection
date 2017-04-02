# 快速入门 #
我们现在就创建一个简单的API，允许管理员在系统中查看和编辑用户及组。

# 创建项目 #
创建一个Django项目叫做tutorial，然后新建一个app叫quickstart。

```
# 创建项目所属文件夹
mkdir tutorial
cd tutorial

# 创建虚拟环境
source env/bin/activate  # windows上使用 `env\Scripts\activate`

# 在虚拟环境中安装Django和Django REST framework
pip install django
pip install djangorestframework

# 创建一个Django项目，并只有一个app
django-admin.py startproject tutorial .  # 注意末尾的"."
cd tutorial
django-admin.py startapp quickstart
cd ..
```
现在进行第一次数据库的同步：
```
python manage.py migrate
```
同时也创建一个超级用户admin，密码为password123。我们在后面的认证中会用到。
```
python manage.py createsuperuser
```
一旦你已经完成数据库的同步并且创建了超级用户，那么我们就可以开始进入项目文件夹敲代码了。。。^_^

# 序列化 #
第一步，我们需要定义一些序列化类。先在/tutorial/quickstart/下创建一个新的模块serializers.py，在表现数据的时候会用到。
```
from django.contrib.auth.models import User, Group
from rest_framework import serializers


class UserSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = User
        fields = ('url', 'username', 'email', 'groups')


class GroupSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Group
        fields = ('url', 'name')
```
请注意我们在这几个例子中使用了超链接的关系，每个类都继承HyperlinkedModelSerializer。你也可以用主键和很多其他的关系来搞定，不过超链接是一种很棒的RESTful设计。

# 视图 #
我们要开始写一些视图了，所以打开tutorial/quickstart/views.py开始敲代码吧。
```
from django.contrib.auth.models import User, Group
from rest_framework import viewsets
from tutorial.quickstart.serializers import UserSerializer, GroupSerializer


class UserViewSet(viewsets.ModelViewSet):
    """
    允许用户查看和编辑的API节点
    """
    queryset = User.objects.all().order_by('-date_joined')
    serializer_class = UserSerializer


class GroupViewSet(viewsets.ModelViewSet):
    """
    允许组查看和编辑的API节点
    """
    queryset = Group.objects.all()
    serializer_class = GroupSerializer
```
与其写一堆视图，我们更倾向去把通用的行为写进视图集的类中。
如果我们需要的话，我们可以很容易的把他们分离成独立的视图，而且使用视图集可以保证视图逻辑的组织性和简洁性。
# 路由 #
OK，现在开始编写API的路由，编辑tutorial/urls.py。
```
from django.conf.urls import url, include
from rest_framework import routers
from tutorial.quickstart import views

router = routers.DefaultRouter()
router.register(r'users', views.UserViewSet)
router.register(r'groups', views.GroupViewSet)


# 我们使用自动的路由分发，并添加了登录的路由
urlpatterns = [
    url(r'^', include(router.urls)),
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]
```
因为我们使用了视图集来代替普通视图，这样子我们通过router这个类注册视图集以便自动的为API生成路由。
另外，如果我们需要对API的路由做更多的控制，我们也可以通过继承views这个基类，把路由的配置写的更明了。
最后，在可视化的API中，我们已经为用户默认添加了登录和登出的视图。当你想要使用可视化的API，并且需要认证的时候，这玩意就派上用场了。
# 设置 #
我们现在需要进行一些全局的设置了。我们想要搞个分页的功能，并且只想让我们的API被管理员调用。这些设置都需要在tutorial/settings.py中进行。
```
INSTALLED_APPS = (
    ...
    'rest_framework',
)

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAdminUser',
    ],
    'PAGE_SIZE': 10
}
```
Bingo! 搞定！

***

# 测试API #
我们现在需要测试我们创建的API，让我们在命令行中启动服务吧。
```
python manage.py runserver
```
我们可以调用我们的API，通过命令行的方式。比如使用工具（命令）curl：
```
bash: curl -H 'Accept: application/json; indent=4' -u admin:password123 http://127.0.0.1:8000/users/
{
    "count": 2,
    "next": null,
    "previous": null,
    "results": [
        {
            "email": "admin@example.com",
            "groups": [],
            "url": "http://127.0.0.1:8000/users/1/",
            "username": "admin"
        },
        {
            "email": "tom@example.com",
            "groups": [                ],
            "url": "http://127.0.0.1:8000/users/2/",
            "username": "tom"
        }
    ]
}
```
或者在命令行使用httpie：
```
bash: http -a admin:password123 http://127.0.0.1:8000/users/

HTTP/1.1 200 OK
...
{
    "count": 2,
    "next": null,
    "previous": null,
    "results": [
        {
            "email": "admin@example.com",
            "groups": [],
            "url": "http://localhost:8000/users/1/",
            "username": "paul"
        },
        {
            "email": "tom@example.com",
            "groups": [                ],
            "url": "http://127.0.0.1:8000/users/2/",
            "username": "tom"
        }
    ]
}
```
或者说直接使用浏览器，通过访问http://127.0.0.1:8000/users/...
![quickstart.png](https://ooo.0o0.ooo/2017/04/02/58e11a350ce16.png)

如果你正在使用浏览器进行访问，那么你需要从右上角的进行登录。
so easy！
如果你想要更加深入的学习并理解REST framework原理，那就好好看官网接下来的文档吧。或者接着看我的系列博客吧。^_^
