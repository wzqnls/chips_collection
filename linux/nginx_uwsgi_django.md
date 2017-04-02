# virtualenv 配置虚拟环境

检测是否已安装virtualenv
```
virtualenv --version
```
若没有显示版本号则表示没有安装virtualenv
安装virtualenv
```
pip install virtualenv
```
创建虚拟环境
路径可自选，也可放置项目目录下
venv为虚拟环境名，可自定义
```
virtualenv venv
```
进入虚拟环境(需在venv当前目录下操作)
```
source /venv/bin/activate

# 退出虚拟环境命令
deactivate
```
安装项目相关依赖
（此操作需要提前提前生成requirements.txt依赖文件）
插入简单说下django依赖文件如何生成
```
生成依赖文件
pip freeze > requirements.txt
```
安装依赖
```
pip install -r requirements.txt
```
如何查看依赖列表
```
pip list
```
至于为什么要用虚拟环境来部署，其中的原因若有疑问就自行谷歌吧。不仅仅是部署，每新建一个项目，都建议使用虚拟环境，好处大大的。
推荐一个很不错的扩展库virtualenvwrapper，挺方便，喜欢的小伙伴就赶紧上手吧。
---
---

**下面就要开始正式的部署了，这篇教程以ubuntu为例**
整个部署结构如下：
the web client <-> the web server(nginx) <-> the socket <-> uwsgi <-> Django
nginx做反向代理处理静态文件，减轻服务器负载
同时也可以配置多台服务器做负载均衡（此篇博客不超扯）
uwsgi处理后台请求
ps：后面写些系列博客介绍下uwsgi和nginx等部署所需
ps：毕竟光讲怎么用，不说原理真是耍流氓
# 配置uwsgi
虚拟环境下安装uwsgi
```
pip install uwsgi

# 若安装失败，可能是python依赖库没有安装
# 执行以下命令
apt-get install python-dev
```
编写测试脚本
在项目目录下添加test.py脚本，添加如下内容
```
# test.py
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"] # python3
    #return ["Hello World"] # python2
```
用test.py测试uwsgi
```
uwsgi --http :8000 --wsgi-file test.py

# 若跑不起来，可能需要添加参数
# 具体原因这里不深究，历史遗留
# 感兴趣的同学可自行谷歌
uwsgi --plugin python,http --http :8000 --wsgi-file test.py
```
浏览器访问localhost:8000查看页面是否显示hello world
若正常显示，则说明如下环节正常拉通
the web client <-> uWSGI <-> Python
接着先测试以下django项目自身能否跑通
```
python manage.py runserver 0.0.0.0:8000
```
确认没问题后,用uwsgi拉通django
```
uwsgi --http :8000 --module mysite.wsgi
```
此处wsgi命名方式：项目名.wsgi
此wsgi文件可在项目目录中得主目录下找到
确认正常运行，说明如下环节正常拉通
```
the web client <-> uWSGI <-> Django
```
# 配置nginx
安装nginx
```
apt-get install nginx
```
测试nginx能否正常运行，启动nginx
```
/etc/init.d/nginx start
# 或者
service nginx start
```
访问浏览器80端口
打开localhost:80
若显示Welcome to nginx!则说明nginx正常运行
说明如下环节正常拉通
```
the web client <-> the web server
```
nginx默认占用80端口
同时访问公网ip，默认访问得端口也是80
即可以直接访问localhost，不需要加80端口号
ps:赠送一些常用nginx命令
```
# 重启nginx
service nginx restart
# 查看nginx运行状态
service nginx status
# 停止nginx服务
service nginx stop
```
编写nginx配置文件（ubuntu下，centos路径则不同）
nginx相关配置存放在/etc/nginx
先将uwsgi_params文件复制到项目目录下
uwsgi_params可在/etc/nginx下找到
```
cp /etc/nginx/uwsgi_params /path/to/your/project
```
然后在项目目录下新建mysite_nginx.conf
配置文件名，自己记得住就好，保证可读性
填入以下内容
```
# myproject_nginx.conf

# the upstream component nginx needs to connect to
upstream django {
    # server unix:///path/to/your/mysite/mysite.sock; # for a file socket
    server 127.0.0.1:8001; # for a web port socket (we'll use this first)
}

# configuration of the server
server {
    # the port your site will be served on
    listen      8000;
    # the domain name it will serve for
    server_name .example.com; # substitute your machine's IP address or FQDN
    charset     utf-8;

    # max upload size
    client_max_body_size 75M;   # adjust to taste

    # Django media
    location /media  {
        alias /path/to/your/mysite/media;  # your Django project's media files - amend as required
    }

    location /static {
        alias /path/to/your/mysite/static; # your Django project's static files - amend as required
    }

    # Finally, send all non-media requests to the Django server.
    location / {
        uwsgi_pass  django;
        include     /path/to/your/mysite/uwsgi_params; # the uwsgi_params file you installed
    }
}
```
写好配置文件后，需要将此配置文件软链接至nginx配置文件目录下
配置文件则存放在/etc/nginx/sites-enabled
```
ln -s /path/to/your/mysite_nginx.conf /etc/nginx/sites-enabled/

# 举例conf文件路径为/home/test/mysite/mysite_nginx.conf
# 命令就这么写
ln -s /home/test/mysite/mysite_nginx.conf /etc/nginx/sites-enabled/
```
接下来拉通部署下静态文件
在django项目得的setting文件中，添加下面一行内容：
```
STATIC_ROOT = os.path.join(BASE_DIR, "static/")
```
运行python命令
```
python manage.py collectstatic
```
此命令会将项目中得所有静态文件全部汇总到static目录下
原因就是为了方便给nginx做反向代理时，可以快速得找到所请求得静态文件
现在可以测试以下nginx是否能够正常访问静态文件
重启以下nginx
```
service nginx restart
```
任意在浏览器访问一个media里面得图片文件（没图片，就自己加一个）
比如访问localhost:8000/media/test.png
若是正常显示，那么接下来得路就好走多了。
然而，意外往往时会发生的，访问不到的就根据报错来定位问题
同时借助nginx日志，来查看问题的原因
nginx日志路径/var/log/nginx/error.log
一般情况下，由于权限问题导致访问失败的可能性最大
这时候就需要修改项目所在目录的权限
所以项目部署的时候不要把代码放到/root等权限敏感的目录下
ok，往下走
这时候就需要拉通nginx和uwsgi了
接着用test.py测试一把
```
uwsgi --socket :8001 --wsgi-file test.py
```
访问8000端口，没有问题的话，说明以下环节也拉通了
the web client <-> the web server <-> the socket <-> uWSGI <-> Python
这时候举例万里长征真的就几步路了
我们之前nginx使用的时tcp sokect转发请求，也就是使用端口转发
现在，换成unix socket转发
unix socket相对tcp socket速度更快，节省端口资源
然而要是做负载均衡的话，就需要利用不同的端口转发请求至处理服务器了
不多说，修改nginx配置文件
```
server unix:///path/to/your/mysite/mysite.sock; # for a file socket
# server 127.0.0.1:8001; # for a web port socket (we'll use this first)
```
然后重启nginx
启动uwsgi
```
uwsgi --socket mysite.sock --wsgi-file test.py
```
访问8000端口，看到hello world，那就是胜利的曙光
然而这里往往是会保权限错误所以，出现权限问题，就试试下面两个命令
其实就加个两个权限参数
```
uwsgi --socket mysite.sock --wsgi-file test.py --chmod-socket=666
# 或者
uwsgi --socket mysite.sock --wsgi-file test.py --chmod-socket=664
```
搞定这个问题后，就可以拉通整个部署结构了(这个权限最保险^_^)
```
uwsgi --socket mysite.sock --module mysite.wsgi --chmod-socket=666
```
好了，其实整个部署过程到这就可以结束了
但是在命令中加各种参数是不太好的一个编码习惯
还是写到配置文件里面去
在项目目录下新建mysite_uwsgi.ini
```
# mysite_uwsgi.ini file
[uwsgi]

# Django-related settings
# the base directory (full path)
chdir           = /path/to/your/project
# Django's wsgi file
module          = project.wsgi
# the virtualenv (full path)
home            = /path/to/virtualenv

# process-related settings
# master
master          = true
# maximum number of worker processes
# 进程数设置与cpu核数相同，保证并行性能
processes       = 10
# the socket (use the full path to be safe
socket          = /path/to/your/project/mysite.sock
# ... with appropriate permissions - may be needed
# chmod-socket    = 664
# clear environment on exit
vacuum          = true
```
然后用简单的uwsgi命令，就能完成部署了
```
uwsgi --ini mysite_uwsgi.ini
```

ps:附赠一些相关项目状态查看命令
```
# 查看某端口占用情况(80端口为例)
lsof -i:80
# 查看uwsgi或者nginx进程
ps -ef | grep uwsgi
# 停止uwsgi主进程
pkill uwsgi
# 杀掉某进程只需要知道pid既可
kill pid
```