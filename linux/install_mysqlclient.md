自从开发全面转向python3之后，由于mysqldb不支持python3，所以django连接mysql就不能再使用mysqldb了。故而选择了mysqlclient，然而两者之间并没有太大的使用上的差异。
很多的小伙伴不太清楚，如何在不同的系统上安装mysqlclient这个API
，今天就简单的讲讲win上和linux上的安装方法。
1.windows
提供一个网站，上面有非常多的适配windows的python库，在这个上面可以找到。
然后直接pip install 就ok了。
[http://www.lfd.uci.edu/~gohlke/pythonlibs/#](http://www.lfd.uci.edu/~gohlke/pythonlibs/#)
有一点要注意：
以下是从这个网站上面检索到的mysqlclient的所有版本
```
Mysqlclient, a fork of the MySQL-python interface for the MySQL database.

    mysqlclient-1.3.9-cp27-cp27m-win32.whl
    mysqlclient-1.3.9-cp27-cp27m-win_amd64.whl
    mysqlclient-1.3.9-cp34-cp34m-win32.whl
    mysqlclient-1.3.9-cp34-cp34m-win_amd64.whl
    mysqlclient-1.3.9-cp35-cp35m-win32.whl
    mysqlclient-1.3.9-cp35-cp35m-win_amd64.whl
    mysqlclient-1.3.9-cp36-cp36m-win32.whl
    mysqlclient-1.3.9-cp36-cp36m-win_amd64.whl
```
cp35代表python3.5的版本，win32代表32位的系统，所以需要选择正确，否则安装过程会报错平台不匹配。

2.linux(centos为例)
这个就简单多了
首先安装一下mysql-devel
```
yum install mysql-devel
```
如果已安装就忽略这一步

然后就可以直接安装mysqlclient了
```
yum install mysqlclient
```
