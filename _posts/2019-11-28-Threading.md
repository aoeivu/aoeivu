---
layout: mypost
title: Theading
categories: [Python]
---

# 创建一个线程
1. 最简单的一个线程
```
from threading import Thread


def count():
    for i in range(10):
        print(i)


if __name__ == '__main__':
    t = Thread(target=count)
    t.start()
```
