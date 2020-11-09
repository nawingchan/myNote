WCS代码优化建议

## 1. except异常处理不规范
在抓取程序异常情况时，使用了`except`指令进行处理，这样做让问题变得模糊，不明确。比如下面这个代码例子：
```
try:
    from wcs_bridge.trans_image import ImageTran
except Exception as e:
    rospy.logerr("[BATCH] Failed import ImageTran")

```
建议的写法：
```
try:
    from wcs_bridge.trans_image import ImageTran
except ImportError as e:
    rospy.logerr("[BATCH] Failed import ImageTran")
```

## 2. batch存放文件的位置问题
测试项目在接口`api/batch`中接受的数据被存放在电脑的绝对目录`/home/xyz/batch_log.json`中，有以下缺点：
(1) 路径命名为绝对路径，如果用户名称有变化，就容易出现文件读取错误
(2) 路径日志文件路径不统一，所有的日志文件最好有专门的存放位置，便于日志查阅
(3) 路径参数是写死的，最好采用一个配置文件进行配置读取。

## 3. 项目的输出信息混乱
当前的测试项目的信息输出方式大概有三种，
(1) print
(2) rospy.logger
(3) asort
但是每次以上述三种方式输出信息时需要重复写三个函数的方法，建议可以写成一个信息输出类，当需要输出时，可直接调用信息输出类里的方法。

## 4. 可选参数的使用
当一个参数可能会传入，也可能不会传入时，可以选择使用<font color=#FF000 >**可选参数**</font>，为可选参数设置一个默认参数在下面代码中：
```
def __init__(self, batch_state, ppHMI):
    self._batch_state = batch_state
    try:
        self._ppHMI = ppHMI('planner')
    except:
        self._ppHMI = None
```
可以使用如下写法：
```
def __init__(self, batch_state, ppHMI=None):
    self._batch_state = batch_state
    if ppHMI:
        self._ppHMI = None
    else:
        self._ppHMI = ppHMI('planner')
```
当然，更好的是让传入的参数合乎规范，这样能减少对异常参数的判断

