---
layout: post
title:  "Python Class"
categories:  Python Class
---

## 类和实例
类是抽象的模板，而实例是根据类创建出来的一个个具体的“对象”
```
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score
    def print_score(self):
        print('%s: %s' % (self.name, self.score))
```
- class 表明定义的是一个类
- Student 是类名
- (object) 表明继承的是object这个类
- __init__方法，在创建实例的时候，把一些我们认为必须绑定的属性强制填写进去
- self，代表创建的实例本身。和普通的函数相比，在类中定义的函数只有一点不同，就是第一个参数永远是实例变量self，并且，调用时，不用传递该参数。
```
bart = Student('Bart Simpson', 59)
bart.age = 17
```
可以自由地给一个实例变量绑定属性，比如，给实例bart绑定一个age属性

## 访问限制
```
class Student(object):

    def __init__(self, name, score):
        self.__name = name
        self.__score = score

    def print_score(self):
        print('%s: %s' % (self.__name, self.__score))

    def get_name(self):
        return self.__name

    def get_score(self):
        return self.__score
```
如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线__，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。
在Python中，变量名类似__xxx__的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量。
以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。
双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问__name是因为Python解释器对外把__name变量改成了_Student__name，所以，仍然可以通过_Student__name来访问__name变量。

## 继承和多态
#### 继承
```
class Animal(object):
    def run(self):
        print('Animal is running...')
```
```
class Dog(Animal):
    pass

class Cat(Animal):
    pass
```
```
dog = Dog()
dog.run()

cat = Cat()
cat.run()
```
子类获得了父类的全部功能。由于Animial实现了run()方法，因此，Dog和Cat作为它的子类，就自动拥有了run()
#### 多态
```
class Dog(Animal):

    def run(self):
        print('Dog is running...')

    def eat(self):
        print('Eating meat...')
```
当子类和父类都存在相同的run()方法时，我们说，子类的run()覆盖了父类的run()，在代码运行的时候，总是会调用子类的run()。这样，我们就获得了继承的另一个好处：多态。
在继承关系中，如果一个实例的数据类型是某个子类，那它的数据类型也可以被看做是父类。
多态真正的威力：调用方只管调用，不管细节，而当我们新增一种Animal的子类时，只要确保run()方法编写正确，不用管原来的代码是如何调用的。这就是著名的“开闭”原则：
- 对扩展开放：允许新增Animal子类；
- 对修改封闭：不需要修改依赖Animal类型的run_twice()等函数。

## 获取对象信息
#### type()
```
type(123)

<class 'int'>
```
```
>>> type(fn)==types.FunctionType
True
>>> type(abs)==types.BuiltinFunctionType
True
>>> type(lambda x: x)==types.LambdaType
True
>>> type((x for x in range(10)))==types.GeneratorType
True
```
#### isinstance()
```
isinstance([1, 2, 3], list)
```
isinstance()判断的是一个对象是否是该类型本身，或者位于该类型的父继承链上。
#### dir()
```
>>> dir('ABC')
['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
```
如果要获得一个对象的所有属性和方法，可以使用dir()函数，它返回一个包含字符串的list。
类似__xxx__的属性和方法在Python中都是有特殊用途的，比如__len__方法返回长度。在Python中，如果你调用len()函数试图获取一个对象的长度，实际上，在len()函数内部，它自动去调用该对象的__len__()方法。
```
>>> hasattr(obj, 'x') # 有属性'x'吗？
True
>>> obj.x
9
>>> hasattr(obj, 'y') # 有属性'y'吗？
False
>>> setattr(obj, 'y', 19) # 设置一个属性'y'
>>> hasattr(obj, 'y') # 有属性'y'吗？
True
>>> getattr(obj, 'y') # 获取属性'y'
19
>>> obj.y # 获取属性'y'
19
```
仅仅把属性和方法列出来是不够的，配合getattr()、setattr()以及hasattr()，我们可以直接操作一个对象的状态。

## 实例属性和类属性
```
class Student(object):
    def __init__(self, name):
        self.name = name

s = Student('Bob')
s.score = 90
```
由于Python是动态语言，根据类创建的实例可以任意绑定属性。
```
class Student(object):
    name = 'Student'
```
绑定类属性  
实例属性的优先级高于类属性。





