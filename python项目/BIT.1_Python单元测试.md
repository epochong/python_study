# Python单元测试

## 单元测试基本概念
单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作。

### 基本思路
比如对函数abs()，我们可以编写出以下几个测试用例：
>* 输入正数，比如1、1.2、0.99，期待返回值与输入相同；
>* 输入负数，比如-1、-1.2、-0.99，期待返回值与输入相反；
>* 输入0，期待返回0；
>* 输入非数值类型，比如None、[]、{}，期待抛出TypeError. 

把上面的测试用例放到一个测试模块里，就是一个完整的**单元测试**

如果单元测试通过，说明我们测试的这个函数能够正常工作. <br>
如果单元测试不通过，要么函数有bug，要么测试条件输入不正确. <br>

### 单元测试的意义
>* 问题发现越早, 解决成本越低
>* 方便对修改过的代码进行回归测试

## 单元测试实例

### 待测试代码
我们来编写一个Dict类，这个类的行为和dict一致，但是可以通过属性来访问，用起来就像下面这样：

	>>> d = Dict(a=1, b=2)
	>>> d['a']
	1
	>>> d.a
	1

mydict.py代码如下：
	
	class Dict(dict):	
	    def __init__(self, **kw):
	        super(Dict, self).__init__(**kw)
	
	    def __getattr__(self, key):
	        try:
	            return self[key]
	        except KeyError:
	            raise AttributeError(r"'Dict' object has no attribute '%s'" % key)
	
	    def __setattr__(self, key, value):
	        self[key] = value

基本思路很简单:
>* 创建一个 Dict 类, 继承自 dict. 这样就能保证大部分行为都和 dict 一致;
>* 实现 `__getattr__` 和 `__setattr__` 方法, 达到按属性取key的效果;
>* 捕捉 KeyError 异常, 并抛出 AttributeError 异常. 因为我们是进行取属性操作, 理应使用 AttributeError 异常.

### unittest模块
为了编写单元测试，我们需要引入Python自带的unittest模块，编写mydict_test.py如下：

	import unittest
	
	from mydict import Dict
	
	class TestDict(unittest.TestCase):
	
	    def test_init(self):
	        d = Dict(a=1, b='test')
	        self.assertEquals(d.a, 1)
	        self.assertEquals(d.b, 'test')
	        self.assertTrue(isinstance(d, dict))
	
	    def test_key(self):
	        d = Dict()
	        d['key'] = 'value'
	        self.assertEquals(d.key, 'value')
	
	    def test_attr(self):
	        d = Dict()
	        d.key = 'value'
	        self.assertTrue('key' in d)
	        self.assertEquals(d['key'], 'value')
	
	    def test_keyerror(self):
	        d = Dict()
	        with self.assertRaises(KeyError):
	            value = d['empty']
	
	    def test_attrerror(self):
	        d = Dict()
	        with self.assertRaises(AttributeError):
	            value = d.empty


代码解释如下:
>* 编写一个测试类，继承unittest.TestCase类.
>* 以test开头的方法就是测试方法，不以test开头的方法不被认为是测试方法，测试的时候不会被执行.
>* 每一个测试用例都需要编写一个test_xxx()方法.
>* 使用unittest.TestCase中的断言完成对结果的检测.

断言概览:
>* assertTrue: 判定参数的值是否是 True;
>* assertEquals: 判定参数的两个变量的值是否相等;
>* assertRaises: 判定语句是否抛出了指定类型的异常; 往往要搭配 with 使用.

### 运行单元测试
一旦编写好单元测试，我们就可以运行单元测试. 

**方法一:** 最简单的运行方式是在mydict_test.py的最后加上两行代码：

	if __name__ == '__main__':
	    unittest.main()

这样就可以把mydict_test.py当做正常的python脚本运行. 

**方法二(推荐):** 在命令行通过参数-m unittest直接运行单元测试：

$ python -m unittest mydict_test

使用方法二, 可以一次批量运行很多单元测试. 同时，有很多工具可以自动来运行这些单元测试.

### setUp与tearDown
可以在单元测试类中编写两个特殊的setUp()和tearDown()方法. 这两个方法会分别在每调用一个测试方法的前后分别被执行. 

>* 在 setUp 中做一些准备工作
>* 在 tearDown 中做一些清理工作

例如: 测试需要启动一个数据库，这时，就可以在setUp()方法中连接数据库，在tearDown()方法中关闭数据库. 


## 总结
Python中的 unittest 使用起来非常简单, 没什么太多注意的.

写单元测试不难, 难的是能够构建起单元测试的思维.