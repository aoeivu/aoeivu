---
layout: mypost
title: 多线程同步中Event和Queue
categories: [Python]
---

## Event
Event存在于threading模块中，如果想要当特定的事件发生后阻塞当前线程，运行其他线程，等待其他线程处理完成后切回当前线程，可以使用Event方法。  
Event()有三种方法：  
```python
from threading import Event

event = Event()
event.clear()  # 重置Event，使所有的Event都属于待命状态
event.wait()  # 阻塞当前线程
event.set()  # 中断当前线程，wait的线程继续运行
```
> 注意：多个事件同时等待某个Event()事件发生，Event()事件发生后所有的线程都会被激活。

### Event()的使用示例  
```python
import time
from threading import Event, Thread


class MyThread(Thread):
    def __init__(self, event):
        super().__init__()
        self.event = event

    def run(self):
        print('Hi, i am waiting Event')
        self.event.wait()
        print('i got it')


if __name__ == '__main__':
    evt = Event()
    t = MyThread(evt)
    t.start()
    print('wait 5 seconds')
    time.sleep(5)
    evt.set()
    evt.clear()
```

## Queue  
从一个线程往另外一个线程发送数据最安全的方式就是使用queue  
queue有两种方法：  
```python
from queue import Queue

q = Queue(maxsize=0)  # maxsize可选默认为0，不受限，一旦>0，而消息数又达到限制，q.put()也将阻塞
queue.put()  # 向队列中发送信息
queue.get()  # 阻塞程序从队列中获取信息
queue.get(timeout=1)  # 阻塞获取信息，超时时间1s
q.join()  # 等待所有的消息都被消费完

q.qsize()  # 查询当前队列的消息个数
q.empty()  # 队列消息是否都被消费完，True/False
q.full()  # 检测队列里消息是否已满
```

> 注意：qsize，empty，full都不是线程安全的，最好不要使用，例如你使用了empty判断当前为空，但是此刻另外一个线程向队列中push了内容。

### Queue()的使用示例
```python
import time
from queue import Queue
from threading import Thread


class Producer(Thread):
    def __init__(self, queue):
        super().__init__()
        self.queue = queue

    def run(self):
        for i in range(10):
            print('I am put {}'.format(i))
            self.queue.put(i)
            time.sleep(0.5)


class Consumer(Thread):
    def __init__(self, queue):
        super().__init__()
        self.queue = queue

    def run(self):
        while True:
            queue_data = self.queue.get()
            print('I got {}'.format(queue_data))


if __name__ == '__main__':
    queue = Queue()
    producer = Producer(queue)
    consumer = Consumer(queue)
    threads = [producer, consumer]
    for thread in threads:
        thread.start()

```