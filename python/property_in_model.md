上篇文章中聊了聊@property的用法，这篇文章则聊聊@property在数据库model中的一些小技巧，同时也会涉及些Django在数据库建模的过程中，外键查询和反向查询方面的内容。
不多说，看例子
```
    from django.db import models

    class Person(models.Model):
        name = models.CharField(max_length=64)
        age = models.IntegerField()
        tel = models.CharField(max_length=64)
        
        @property
        def all_cars(self):
            return cars.all()

        @property
        def info(self):
            # return the name and tel of person
            return '%s %s' % (self.name, self.tel)

    class Car(models.Model):
        owner = models.Foreignkey(Person, related_name='cars')
        name = models.CharField(max_length=64)
        price = models.FloatField()

                
```
在上面的两个表中，Person表是主表，Car是字表，Car表外键至Person表。
子表查询主表：
```
    car = Car.objects.get(id=1)
    # 查询该车的车主
    owner = car.owner

```
主表查询子表，即反向查询：
```
    Tom = Person.objects.get(id=1)
    # 查询此人有多少车
    # 方式一:
    # Django默认每个主表对象都有一个外键的属性
    # 可以通过它来查询所有属于主表的子表信息
    # 查询方式：主表.子表_set()
    # 返回值为一个queryset对象
    Tom.Car_set().all()
    # 方式二：
    # 通过在外键中设置related_name属性值既可
    Tom.cars.all()
    # 方式三：
    # 通过@property装饰器在model中预定义方法实现
    Tom.all_cars()
```
查询一些自己需要的组合数据,比如获取某人的关键信息
```
    Tom.info()
```
另外，在新的需求突然提出的时候，在不改动原有查询代码的情况下，在表模型中添加此装饰器装饰过的方法既可。