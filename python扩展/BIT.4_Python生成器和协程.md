# 生成器和协程

### 本节重点
>* 掌握yield关键字的用法
>* 理解生成器, 迭代器, 可迭代对象的概念和区别
>* 理解协程的概念, 协程和线程的区别
>* 掌握 gevent 库的基本使用方法 

## yield和生成器
yield是Python的一个关键字. 具有 "屌炸天" 的功能.

### yield的发音
咳咳, 老湿我的英文水平是渣渣~~大佬们轻喷.

![](../image/给大佬递茶.jpg)

![](../image/yield发音.png)

这个音标看起来好难~~完全不知道怎么读~~没关系, 我们用词典发音听一听~~

大概读音就是 "右的" . 大家出去面试的时候, 不要读错了~~

出去大家千万别说, 我的英语是Python老湿教的~~~

![](../image/捂脸.jpg)

### yield的用法
我们回忆一下, 曾经我们有一个函数叫做 `range`, 在我们for循环时非常有用. 

	for i in range(0, 10):
		print i

我们看看range到底返回了个啥

	print type(range(0, 10))

	# 执行结果
	<type 'list'>

原来range返回了一个列表. 那么问题来了~~

假如我们需要循环1000w次~~那么就需要先构造一个1000w个元素的列表. 但是呢, 循环结束之后, 这个列表又立刻不需要了. 这无疑是一个巨大的开销.

那么我们来自己实现一个myrange函数, 来解决这个内存开销的问题~~

	def myrange(start, stop=None, step=1):
	    if stop == None:
	        stop = start
	        start = 0
	    while start < stop:
	        yield start
	        start += step
	
	print 'test1'
	for i in myrange(10):
	    print i
	
	print 'test2'
	for i in myrange(0, 10):
	    print i
	
	print 'test3'
	for i in myrange(0, 10, 2):
	    print i

yield到底做了什么事情?
>* 执行到yield语句的时候, 函数会立刻返回.
>* 但是函数的返回同时, 保留了现场! 就是这个函数知道自己刚刚运行到哪里.
>* 于是第二次再调用到myrange函数时, 上次执行的现场又恢复了(包括现场的所有局部变量, 指令的pc指针等).
>* 于是又会沿着之前的while循环继续执行, 直到下一次又执行到yield语句再次退出并保存现场.
>* 一直执行到循环结束, 函数执行退出, 这个时候for循环也会跟着退出.

这样, 通过反复调用函数的方式, 虽然没有创建一个大列表, 但是通过反复调用函数的方式, 一样能够把要迭代的序列都获取到. 这样的操作, 称为 **生成器**.

### 关于生成器
生成器不是Python独有的特性, C++也可以很容易实现一个生成器.

	struct Generator {
		Generator() : inited(false), current(0) {}
	
		int operator(int start, int end, int step = 1) {
			if (inited == false) {
				current = start;
				inited = true;
			}
			if (current < end) {
				return current++;
			}
		}

		bool inited;
		int current;
	};

>* 借助函数对象的方式, 可以把函数调用的状态信息保存起来. 从而在第二次调用的时候可以从上次执行的位置继续执行.

### yield究竟返回了什么?

我们仔细看看到底yield返回的是什么.

	def myrange(start, stop=None, step=1):
	    if stop == None:
	        stop = start
	        start = 0
	    while start < stop:
	        yield start
	        start += step
	
	print type(myrange(10))

	# 执行结果
	<type 'generator'>

>* 如果一个函数中, 带有yield关键字, 那么这个函数就变成了一个 **生成器**. 它的执行行为和普通函数就不太相同了.
>* 生成器和列表等序列一样, 也可以使用for循环进行遍历. 
>* 这样的可以在for循环中遍历的对象称为**可迭代对象**, 也叫 **迭代器**

### 生成器
还是刚才的那个例子, 我们用内建方法dir看看这个生成器中有哪些属性.

	def myrange(start, stop=None, step=1):
	    if stop == None:
	        stop = start
	        start = 0
	    while start < stop:
	        yield start
	        start += step

	print dir(myrange(10))

	# 执行结果
	['__class__', '__delattr__', '__doc__', '__format__', '__getattribute__', '__hash__', '__init__', '__iter__', '__name__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'close', 'gi_code', 'gi_frame', 'gi_running', 'next', 'send', 'throw']
 
虽然属性很多, 我们只关注其中最重点的一个属性, 叫做 `next`. 我们来实验一下next的具体行为.

	def myrange(start, stop=None, step=1):
	    if stop == None:
	        stop = start
	        start = 0
	    while start < stop:
	        yield start
	        start += step
	
	a = myrange(3)
	print a.next()
	print a.next()
	print a.next()
	print a.next()	

	# 执行结果
	0
	1
	2
	Traceback (most recent call last):
	  File "D:\code\python\test\test.py", line 13, in <module>
	    print a.next()
	StopIteration
	
>* 对于一个生成器对象, 每次调用一下next方法, 就返回出一个生成的值.
>* 到达边界末尾, 再次调用next方法的时候, 就会抛出一个StopIteration异常. 而for循环自动处理了这个异常. 捕捉到这个异常时认为循环终止.
>* 当然了, 我们一般不会手动调用next, 都是使用for循环来处理生成器对象的迭代.

除了这种函数形式的生成器, 还有一种	**生成器表达式**, 即通过一个表达式来创建个生成器.

	gen = (x ^ 2 for i in xrange(3))

	for i in gen:
		print i

	# 执行结果
	0
	1
	4

这个语法和列表推导非常相似, 只是用圆括号()来表示生成器表达式, 使用方括号[]表示列表推导.

其实Python中有个内建函数 `xrange` , 使用方法和 `range` 相同. 唯一的区别就是 `range` 返回列表, `xrange` 返回一个生成器(和我们的myrange函数一样).<br>
因此推荐使用 `xrange` 代替 `range`.	

### 可迭代对象和迭代器
我们知道, for循环中, 可以放两种类型的对象:
>* 序列/集合类型的对象, string, list, tuple, dict等;
>* 生成器, 包括生成器表达式和带有yield的函数;
能够被for循环遍历的对象, 称为 **可迭代对象 Iterable**

可以使用isinstance()判断一个对象是否是Iterable对象

	>>> from collections import Iterable
	>>> isinstance([], Iterable)
	True
	>>> isinstance({}, Iterable)
	True
	>>> isinstance('abc', Iterable)
	True
	>>> isinstance((x for x in range(10)), Iterable)
	True
	>>> isinstance(100, Iterable)
	False

同时我们刚刚讲了, 生成器可以使用next获取到下一个元素的位置, 直到最后抛出StopIteration异常.

>* 可以被next()函数调用并不断返回下一个值的对象称为 **迭代器 Iterator**

可以使用isinstance()判断一个对象是否是Iterator对象

	>>> from collections import Iterator
	>>> isinstance((x for x in range(10)), Iterator)
	True
	>>> isinstance([], Iterator)
	False
	>>> isinstance({}, Iterator)
	False
	>>> isinstance('abc', Iterator)
	False

生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator.

把list、dict、str等Iterable变成Iterator可以使用iter()函数

	>>> isinstance(iter([]), Iterator)
	True
	>>> isinstance(iter('abc'), Iterator)
	True

>* Python的迭代器, 只能next往后遍历. 相当于C++中的前向迭代器.

### for循环的本质
for循环本质上就是通过不断调用next()函数实现的

	for x in [1, 2, 3, 4, 5]:
    	pass

等价于

	# 首先获得Iterator对象:
	it = iter([1, 2, 3, 4, 5])
	# 循环:
	while True:
	    try:
	        # 获得下一个值:
	        x = next(it)
	    except StopIteration:
	        # 遇到StopIteration就退出循环
	        break

### 理解可迭代对象和迭代器的区别
为什么list, dict, str等数据类型是可迭代对象而不是迭代器?
>* Iterator对象表示的是一个数据流. 
>* Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误; 
>* 可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据;
>* 所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算;

Iterator甚至可以表示一个无限大的数据流，例如全体自然数. 而使用list是永远不可能存储全体自然数的.

### 理解生成器和迭代器的区别
我们要回过头来理解一下Python的**鸭子类型**

只要一个对象, 具备 `__iter__` 成员和 `next` 成员, 并且next成员迭代结束能够抛出StopIteration异常, 那么这个对象就可以认为是一个迭代器对象.

生成器具备这样的特点, 因此可以认为生成器是一个迭代器. 

如果同学们学过C++或者Java, 那么这几个概念就非常好理解了. 
>* 在C++或者Java中, 容器就是可迭代对象. 迭代器就是迭代器.

因为Python中内置了生成器这个既和可迭代对象用法相似, 又具备迭代器特性的存在, 才导致这几个概念容易混淆.

## 认识协程

### 协程的概念
协程，又称微线程，纤程; 英文名Coroutine.

协程的概念很早就提出来了，但直到最近几年才在某些语言（如Lua, Golang）中得到广泛应用.

>* 函数，或者称为子程序，在所有语言中都是层级调用，比如A调用B，B在执行过程中又调用了C，C执行完毕返回，B执行完毕返回，最后是A执行完毕.
>* 函数调用过程中会维护一个调用栈，每一个线程都有自己的调用栈.
>* 函数调用总是一个入口，一次返回，调用顺序是明确的. 而协程的调用和函数不同.

协程看上去也是函数，但执行过程中，在函数内部可中断，然后转而执行别的函数，在适当的时候再返回来接着执行. 

### 协程的应用场景
协程特别适用于一段逻辑中包含一定的IO操作. 

回忆一下IO, IO操作包含两个部分:
>* 等待
>* 拷贝

在这两个阶段, 其实CPU都是空闲的, 完全可以利用这段CPU空闲时间去做一些其他的事情.
>* 我们之前可以通过多线程的方式, 使CPU资源更充分的利用.
>* 但是线程切换, 是由操作系统进行调度的. 需要有一定的开销.
>* 但是协程是在同一个线程内, 进行函数级别的切换, 这个开销比线程开销小很多.

### 协程的优点
最大的优势就是协程极高的执行效率. 
>* 函数切换不是线程切换，而是由程序自身控制. 
>* 因此，没有线程切换的开销，和多线程比，线程数量越多，协程的性能优势就越明显.

第二大优势就是不需要多线程的锁机制.
>* 只有一个线程，也不存在同时写变量冲突，在协程中控制共享资源不加锁. 所以执行效率比多线程高很多.

因为协程是一个线程执行，那怎么利用多核CPU呢？最简单的方法是多进程+协程，既充分利用多核，又充分发挥协程的高效率，可获得极高的性能.

## gevent库
对于Python来说, yield其实有了一点协程的味道(同一个线程内切换函数), 但是还远远不够. 在Python2中, gevent库提供了更全面的协程特性的支持.

### gevent安装
gevent是一个第三方库, 而不是标准库. 需要先安装才能使用.

回顾pip的使用方法

	pip install gevent

### gevent的使用
gevent是基于greenlet(Python另外的一个协程库)实现的. 相比于greenlet, gevent的特点是不需要手动切换函数, 可以在函数中出现IO操作的时候自动切换.

	import gevent
	from gevent import monkey
	monkey.patch_all()
	
	def Func(name):
	    for _ in range(5):
	        print name
	
	g1 = gevent.spawn(Func, 1)
	g2 = gevent.spawn(Func, 2)
	g3 = gevent.spawn(Func, 3)
	g1.join()
	g2.join()
	g3.join()	

	# 执行结果
	1
	1
	1
	1
	1
	2
	2
	2
	2
	2
	3
	3
	3
	3
	3

>* monkey.patch_all() 是因为gevent需要对Python标准库的IO函数做一些修改, 以支持在程序发生IO时自动切换协程.
>* 上面的代码中我们看到是顺序执行的. 虽然print也是IO操作, 但是不需要耗时等待, 所以并gevent也没有进行无脑的切换.

我们可以手动使用 gevent.sleep(0) 交出当前协程的控制权.

	import gevent
	from gevent import monkey
	monkey.patch_all()
	
	def Func(name):
	    for _ in range(5):
	        print name
			gevent.sleep(0)
	
	g1 = gevent.spawn(Func, 1)
	g2 = gevent.spawn(Func, 2)
	g3 = gevent.spawn(Func, 3)
	g1.join()
	g2.join()
	g3.join()	

	# 执行结果
	1
	2
	3
	1
	2
	3
	1
	2
	3
	1
	2
	3
	1
	2
	3

>* 当使用 gevent.sleep(0) 交出控制权时, gevent会自动的切换到下一个协程执行. 
>* 如果下一个协程不主动交出控制权, 或者不遇到耗时的IO操作(主要是网络上的IO), 那么就会一直执行下去.

在C语言中, 也可以用 sleep(0) 这样的函数交出线程的控制权. 只不过线程的调度是由操作系统完成的, 

### 基于gevent的网络爬虫
具体的细节我们后面结合具体场景再详细讲. 此处先留个悬念.

## Python3的协程(选学)
查找资料, 了解Python async/await 的用法. 
