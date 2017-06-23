---
title: 廖雪峰 python notes
tags: python
notebook: Python
---
<!-- MarkdownTOC -->

- [Python基础](#python%E5%9F%BA%E7%A1%80)
- [函数](#%E5%87%BD%E6%95%B0)
- [高级特性](#%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7)
- [函数式编程](#%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BC%96%E7%A8%8B)
- [面向对象](#%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1)

<!-- /MarkdownTOC -->


## Python基础
1. 关于可变变量与不可变变量
  - str是不可变变量，e.g. ,
    ```python
    a='abc'
    a.replace('a','A') # 输出 'Abc'， 但是a还是 'abc'。a 是一个变量，replace 是作用在字符串上面，并没有改变变量 a 的值
    ```
  - list 是可变变量，e.g. ,
    ```python
    a=['c','b','a']
    a.sort() # ['a','b','c']
    ```
2. python 翻转字符串：a[::-1] , step 设定为-1时是倒着数的, 不会有a[1:4:-1],只能是a[4:1:-1]

## 函数
1. 空函数
    ```python
    def my_abs(x):
      pass
    ```
2. 参数检查
    ```python
    isinstance(x,(int,float))
    ```
3. 函数返回的多个值其实是一个tuple
4. 位置参数和默认参数
    ```python
    def power(x,n=2):
      pass
    ```
5. 可变参数：参数数量可变(0或者任意个参数), **传入的实际是一个list或者tuple**
    ```python
    def calc(*numbers):
      sum = 0
      for n in numbers:
        sum = sum + n * n
      return sum
    ```
    ```python
    nums = [1, 2, 3]
    calc(nums[0], nums[1], nums[2])
    ```
    or
    ```python
    nums = [1, 2, 3]
    calc(*nums)
    ```
6. 关键字参数：关键字参数允许传入0个或者任意个含 **参数名** 的参数，这些关键字参数在函数内部被自动组成一个dict
    ```python
    def person(name, age, **kw)
      print('name':name,'age':age)

    >>> person('Bob', 35, city='Beijing')
    name: Bob age: 35 other: {'city': 'Beijing'}

    >>> person('Adam', 45, gender='M', job='Engineer')
    name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}

    >>> extra = {'city': 'Beijing', 'job': 'Engineer'}
    >>> person('Jack', 24, **extra)
    name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
    ```
7. 命名关键字参数：限制关键字参数的名字, 但是需要\*来分隔位置参数和命名关键字参数
    ```python
    def person(name,age,*,city,job):
      pass
    ```
    如果函数中已经有一个可变参数，就不需要\*了
    ```python
    def person(name,age,*args,city,job):
      pass
    ```
8. 总结
    - 参数定义的顺序必须是：必选参数，默认参数，可变参数，命名关键字参数，关键字参数
    - \*args是可变参数， args接收的实际是一个list或者tuple
    - \*\*kw是关键字参数， kw 接收的实际是一个dict
    - 可变参数既可以直接传入：func(1, 2, 3)，又可以先组装list或tuple，再通过\*args传入：func(\*(1, 2, 3))
    - 关键字参数既可以直接传入：func(a=1, b=2)，又可以先组装dict，再通过\*\*kw传入：func(\*\*{'a': 1, 'b': 2})
    -
9. 递归函数调用次数过多可能会导致栈溢出

## 高级特性
1. 切片
    - L[0:3] 和 L[:3] 都表示取前3个元素
    - L[::5] 每5个一取
    - L[::-1] reverse
2. 迭代
    - enumerate() 实现对index的循环
3. 列表生成
    - 普通方法
      ```python
        a=list(range(1,11))
      ```
    - 列表生成式
      ```python
        a=[x*x for x in range(1,11)]
        a=[x*x for x in range(1,11) if x%2==0]
      ```
4. Generator: 列表容量有限，生成元素数量很大的列表内存空间需求太大。可以用generator保存推算列表元素的算法，这样就不用创建完整的list，从而节省空间。
    - 创建generator
      ```python
      a=(x*x for x in range(1,11))
      for i in a:
        print(i)
      ```
    - 函数generator: 函数是顺序执行，而generator是每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句开始执行。
      ```python
      def fib(max): #裴波那契数列
        n,a,b=0,0,1
        while n<max:
          yield b #print(b)
          a,b=b,a+b
          n=n+1
        return 'done'
      ```
      ```python
      >>>a=fib(3)
      >>>next(a)
      1
      >>>next(a)
      1
      >>>next(a)
      2
      ```
5. 迭代器
    - 凡是可以作用于for循环的对象都是Iterable类型
    - 凡是可以作用于next()对象的都是Iterator类型，这是一个惰性计算的序列
    - 集合数据类型如list，dict，str是Iterable但是不是Iterator，不过可以通过iter(Iterable)获得一个Iterator对象, 通过list(Iterator) 获得Iterable 对象

## 函数式编程
1. 变量可以指向函数
    ```python
    >>>f=abs
    >>>f(-10)
    10
    ```
2. 函数名也是变量
3. 传入函数： 将一个函数作为另一个函数的参数
    ```python
    def add(x,y,f):
      return f(x)+f(y)
    ```
    这里也可以用可变参数，可以传入多个函数！
4. map(func,Iterable) map 接收两个参数，一个function，一个Iterable，然后将function 作用在Iterable里面每个元素，并返回一个 **Iterator** 对象
5. reduce(func, Iterable or Iterator) reduce 也接收两个参数，一个必须接收两个参数的function，一个序列，然后reduce把结果序列和序列的下一个元素做累积运算
    - 一个str2int的函数
        ```python
        from functools import reduce
        def str2int(x):
          def fn(x,y):
            return 10*x+y
          def char2num(s):
            return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[s]  #返回dict里key对应的value
          return reduce(fn, map(char2num,x)) #map 将str变成一个个num（存放int的Iterator）， 然后reduce将Iterator变成一个int对象
        ```
6. filter(func, Iterable or Iterator) 把传入的函数作用于序列的每个元素，根据返回值是'True'或者'False'决定保留还是丢弃该元素，返回 Iterator
7. lambda function，匿名函数， 经常和map(), reduce(), filter() 一起用。
    ```python
    lambda x: x*x
    def f(x):
      return x*x
    ```
    >Of course, we could define a separate function somewhere else and then use that function's name as an argument to filter() etc., and in fact that's probably a good idea if we're going to use that function several times, or if the function is too complex for writing in a single line. However, if we need it only once and it's quite simple (i.e. it contains just one expression, like in the above examples), it's more convenient to use a lambda construct to generate a (temporary) anonymous function and pass it to filter() immediately. This creates very compact, yet readable code.
8. sorted(list, key, reverse=False) 可以对list, str, dict 等排序, key函数可以实现自定义排序
    ```python
    #L = [('Bob', 75), ('Adam', 92), ('Bart', 66), ('Lisa', 88)]
    >>>sorted(L,key=lambda l: l[0])
    [('Adam', 92), ('Bart', 66), ('Bob', 75), ('Lisa', 88)]
    >>>sorted(L,key=lambda l: l[1])
    [('Bart', 66), ('Bob', 75), ('Lisa', 88), ('Adam', 92)]
    ```
9. 函数可以作为返回值。。。
10. 装饰器 decorator：希望增加函数的功能，但是又不希望去改变其定义，可以通过decorator动态改变其功能
    ```python
    def log(func):
      @functools.warps(func) #保证原始函数的_name_属性不变
      def wrapper(*args, **kw):
        print('call %s:' % func._name_)
        return func(*args, **kw)
      return wrapper

    @log # 将decorator置于函数定义处
    def now():
      print('19/5/2017')
    ```
    在调用now()时，相当于调用了now=log(now)
11. partial(function, ) 将原函数的部分参数固定住

## 面向对象
1. private 变量： 双下划线开头, __name, 外部无法直接访问，可以设置 get(),set() 等方法来获取值和修改
2. __xxx__特殊变量，可以访问
3. _xxx 虽然可以直接访问，但是请把我当成私有变量，不要访问。。。
4. 注意下面这种错误写法
    ```python
    >>>bart=Student('Bart Simpson', 98)
    >>>bart.__name='New name' #这里的__name 变量和class 内部的__name不是一个变量，class内部的变量早就被修改成了_Student__name,所以这里是给bart增加了一个__name 变量
    >>>bart.__name
    'New name'
    >>>bart.get_name()
    'Bart Simpson'
    ```
5. 继承和多态：继承可以把父类的所有功能都直接拿过来，这样就不必重零做起，子类只需要新增自己特有的方法，也可以把父类不适合的方法覆盖重写。
6. type() 判断对象的类型。dir() 获取一个对象的所有属性和方法
