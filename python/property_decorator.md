1.@property是什么？
@property是python内置的一个装饰器，作用是将一个方法变成属性，具体的装饰器实现过程比较的复杂，这里不过多深入，这篇文章仅仅是针对@property的上层用法的一个讲解。
2.如何使用@property？
property函数原型为property(fget=None,fset=None,fdel=None,doc=None)
好吧，其实光看函数原型还是不太好掌握用法，来个实际的例子。
```
    # 这是不使用@property的类
    class Boy(object):
        def __init__(self, name):
            self.name = name

        def get_name(self):
            return self.name

        def set_name(self, new_name):
            self.name = new_name

        def del_name(self):
            del self.name

```
获取boy的name，改动name，显示name都需要调用不同的函数。
```
    boy = Boy('Tom')
    print(boy.get_name) 获得 'Tom'
    # 下面更改一下Tom的名字
    boy.set_name('Alice')
    print(boy.get_name()) '获得' 'Alice'
    # 删除name
    boy.del_name()
```

```
    # 使用@property实现上述类
    class Boy(object):
        def __init__(self, name):
            self.name = name
        
        @property
        def name(self):
            return self.name
        
        # 下面这个装饰器是由上面的@property衍生出来的装饰器
        @name.setter
        def name(self, new_name):
            self.name = new_name

        # 下面这个装饰器是由上面的@property衍生出来的装饰器
        @name.deleter
        def del_name(self):
            del self.name
```
对这个类进行实例化
```
boy = Boy('Tom')
# 更新name
boy.name = 'Alice'
print(boy.name) # 获得'Alice'
# 删除name
del boy.name
```
有没有一种一气呵成的感觉。
纸上得来终觉浅，须知此事要躬行。自己尝试着将这个装饰器用起来，理解的速度回更快，下篇文章分享一下@property在数据库model中的一些小技巧。
