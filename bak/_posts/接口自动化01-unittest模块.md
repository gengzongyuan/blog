title: 接口自动化01 unittest模块
date: 2018-09-21 17:33:09
tags: [自动化测试,接口自动化]
categories: [测试]
---


unittest是一个具有查找用例、执行用例、校验结果、产生用例的模块，能够极大的方便我们的自动化测试

<!--more-->

--------
**安装**
```
pip intall unittest
```
**测试用例及测试集合**
- unittest中的方法
```
# 被测方法
def calc(a,b):
    return a//b
```
```
# 创建一个测试类，继承自unittest.TestCase
class test(unittest.TestCase):
    # 每个case的前置动作
    def setUp(self):
        print('set up!')

    # 所有case的前置动作，可以将数据库连接放到里面
    @classmethod
    def setUpClass(cls):
        print("setUpClass")

    # 测试case
    # equal断言
    def test_case1(self):
        print("test_cast1")
        res = calc(4,2)
        self.assertEqual(res,2,msg='expect is not equal 2') # res == 2

    # 测试case
    # in断言
    def test_case2(self):
        print("test_cast2")
        res = calc(5,1)
        self.assertIn(res,[5,2],msg='expect is not equal 5 or 2')# res in [5,2]

    # 每个case的后置动作
    def tearDown(self):
        print('tearDown!!')

    # 所有case的后置动作
    @classmethod
    def tearDownClass(cls):
        print("tearDownClass")
    
    # 后面会讲到
    def say(self):
        print("say")
```
- unittest.main()
```
if __name__ == '__main__':
    # main()方法使用TestLoader类来搜索所有包含在该模块中以“test”命名开头的测试方法，并自动执行他们。
    # 这事我们可以看到say函数并没有被执行到
    unittest.main()
```
- unittest.TestSuite()--测试集合
- testsuit相当于一个测试集合，如果我们有10个测试类，现在我们想执行其中的5个测试类，这时就可以用到测试集合了
```
suite = unittest.TestSuite() # 定义一个测试用例集合
suite.addTest(test('say'))  # 将test的say方法添加到测试类中
suite .addTest(unittest.makeSuite(test))  # 将类中的所有测试方法添加到测试集合中(所有以test开头)
```

**测试执行器**
- unittest.TextTestRunner()--文本执行器
```
# 测试的执行结果会实时的打印在控制台上
runner = unittest.TextTestRunner()
runner.run(suite)
```
- HTMLTestRunner.HTMLTestRunner--HTML执行器
```
pip install HTMLTestRunner
```
```
re = open('report.html','wb')
runner = HTMLTestRunner.HTMLTestRunner(stream=re,
                                       title="测试标题",
                                       description="大树的测试报告描述")
runner.run(test_suite)
re.close()
```
![report.html](https://upload-images.jianshu.io/upload_images/2572206-cef34d1413c77122.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- BeautifulReport--好看的HTML测试执行器
```
链接：https://pan.baidu.com/s/1W04YSu7FgoaoTDlIe-tgAw 密码：tgln
解压后将BeautifulReport拖到自己的环境变量中
```
```
report = BeautifulReport.BeautifulReport(test_suite)
report.report(description="大树的测试报告",filename='report3.html')
```
```
如果想要在报告中显示每条用例的注释
    def test_case2(self):
        '''这是一个测试用例描述''' # 添加这一行
        print("test_cast2")
        res = calc(5,1)
        self.assertIn(res,[5,2],msg='expect is not equal 5 or 2')# res in [5,2]
```
![report3](https://upload-images.jianshu.io/upload_images/2572206-bb044061cd7014ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

