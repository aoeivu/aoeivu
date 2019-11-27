---
layout: mypost
title: Theading
categories: [Python]
---

# 创建一个线程
1. 最简单的一个线程  
```python
from threading import Thread


def count():
    for i in range(10):
        print(i)


if __name__ == '__main__':
    t = Thread(target=count)
    t.start()

# 运行结果：
# 0,1,2,3...
```
2. 继承Thread创建一个线程  
```python
from threading import Thread


class MyThread(Thread):
    def __init__(self, num):
        super().__init__()  # 或写成 Thread.__init__(self)
        self.num = num

    def run(self):
        for i in range(self.num):
            print(i)


if __name__ == '__main__':
    t = MyThread(10)
    t.start()

# 运行结果：
# 0,1,2,3...
```
3. 关于thread.join()方法  
阻塞当前线程，待当前线程结束后再往下执行  

主线程和一个子线程  
```python
import time
from threading import Thread


def count():
    for i in range(10):
        print(i)
        time.sleep(0.5)


if __name__ == '__main__':
    t = Thread(target=count)
    t.start()
    t.join()
    print('hhh')

# 运行结果 1,2,3,4,5...hhh
```

两个线程顺序执行  
```python
import time
from threading import Thread


def count_up():
    for i in range(10):
        print(i)
        time.sleep(0.5)


def count_down():
    for i in range(10):
        i = 10 - i
        print(i)
        time.sleep(0.5)


if __name__ == '__main__':
    t1 = Thread(target=count_up)
    t2 = Thread(target=count_down)
    t1.start()
    t1.join()
    t2.start()
    t2.join()

# 运行结果0,1,2,3,4,5,6,7,8,9,10,9,8....
```
4. setDamon(True)  
setDamon值默认为False，当setDamon=True的时候，不管子线程有没有结束，只要主线程结束了，子线程就结束  
注意:  
a. setDamon=True必须放在start()函数之前  
b. setDamon=True既可以写t.setDamon(True),也可以Thread(target=count, daemon=True)  
```python
import time
from threading import Thread


def count():
    for i in range(10):
        print(i)
        time.sleep(0.5)


if __name__ == '__main__':
    t = Thread(target=count)
    t.setDaemon(True)
    t.start()
    print('主线程开始，等待2S子线程强制结束')
    time.sleep(2)
    print('主线程结束，子线程结束')
```
5. conclusion  
```python
t = Thread(target=func, args=(1, 2))  # func不可包含()，args为turple类型
t.start()  # 开始一个子线程
t.join()  # 阻塞其他线程，当前线程运行结束后继续执行
t.setDaemon(True)  # 子线程随着主线程退出而退出，默认False
t.name = 'MyThread'  # 给当前线程命名
t.getName()  # 获取当前线程名称
```