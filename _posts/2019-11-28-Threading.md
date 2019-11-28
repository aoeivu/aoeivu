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
阻塞当前线程，待当前线程结束后再往下执行  主线程或者另一个子线程  

   a. 子线程阻塞，待子线程结束之后，主线程继续执行

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
    a. 第一个子线程执行结束后第二个子线程继续执行

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

# Lock()

1. Lock简介  
    Lock：当不同的线程会对同一个变量进行操作的时候，如果不能正确控制先后的顺序，则可能会导致变量的值造成不可预估的错误。  

    Lock有以下两种方法：  

    ```python
    from threading import Lock

    lock = Lock()
    lock.acquire()  # 获取锁
    lock.release()  # 释放锁
    ```

    当有两个线程同时运行时，假设一个变量 X 首先需要被函数A处理，然后再递交给函数B处理，则：  
    a. 在A中首先获取锁lock.acquire()，待A函数处理完成后lock.release()释放锁  
    b. B函数获取A函数释放的锁lock.acquire()，B函数使用完成后释放锁

2. 示例  
    a. 没有使用锁

    ```python
    from threading import Thread


    def lock_test_1():
        global variable
        for _ in range(10):
            variable += 1
            print(variable)


    def lock_test_2():
        global variable
        for i in range(10):
            variable += 10
            print(variable)


    if __name__ == '__main__':
        variable = 0
        threads = []
        t1 = Thread(target=lock_test_1)
        t2 = Thread(target=lock_test_2)
        threads.append(t1)
        threads.append(t2)
        for thread in threads:
            thread.start()

    # 只要运行足够多的次数，运行的结果就会不一样
    # 期望结果 1，2，3，4，5，6，7，8，9，10，20，30...
    # 实际可能结果 1，11，12...
    ```

    b. 使用锁后

    ```python
    import time
    from threading import Thread, Lock


    def lock_test_1():
        global variable, lock
        lock.acquire()
        for _ in range(10):
            variable += 1
            print(variable)
        lock.release()


    def lock_test_2():
        global variable, lock
        lock.acquire()
        for i in range(10):
            variable += 10
            print(variable)
        lock.release()


    if __name__ == '__main__':
        variable = 0
        lock = Lock()
        threads = []
        t1 = Thread(target=lock_test_1)
        t2 = Thread(target=lock_test_2)
        threads.append(t1)
        threads.append(t2)
        for thread in threads:
            thread.start()

    # 运行结果永远是 1，2，3，4，5，6，7，8，9，10，20，30...
    ```

3. 注意事项  
    a. 假设AB两个线程，A没有释放锁，B想获取锁，A一直没有释放，B一直就在获取锁的状态，如果A一直不释放锁，就会导致 “死锁”。  
    b. with语句可以自动获取锁，在with代码块结束释放锁

    ```python
    from threading import Thread, Lock

    lock = Lock()
    with lock:
        ...
    ```

