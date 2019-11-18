---
layout: mypost
title: format buildin function
categories: [Python]
---

## 普通用法
```python
'{} {}'.format('hello', 'world')    # hello world
'{1} {0}'.format('world', 'hello')  # hello world

```

## 数字的格式化输出
```python
# 数字的格式化输出
x = 1234.5678
format(x, '.2f')       # 1234.57
format(x, '>10.2f')    # "   1234.57"
format(x, '^10.2f')    # " 1234.57  "
format(x, ',')         # 1,234.5678
format(x, '^10,.2f')   # " 1,234.57 "
'{:^10.2f}'.format(x)  # " 1234.57  "
```


## 根据变量名格式化
```python
website_name = '百度'
website_url = 'www.baidu.com'
# 输出：百度 www.baidu.com
f"{website_name} {website_url}"  # python3.6新增f-string方法
"{website_name} {website_url}".format(website_name=website_name, website_url=website_url)
```

## 利用字典格式化输出
```python
website_dic = {'website': '百度', 'url': 'www.baidu.com'}
'{website} {url}'.format(**website_dic)  # 百度 www.baidu.com
```

## 填充和对齐操作
```python
format('hello', '^20')    # 居中 "       hello        "
format('hello', '>20')    # 不足位，左填充 "               hello"
format('hello', '<20')    # 不足位，右填充 "hello               "

'{:^20}'.format('hello')  # 居中 "       hello        "
'{:>20}'.format('hello')  # 不足位，左填充 "               hello"
'{:<20}'.format('hello')  # 不足位，右填充 "hello               "

format('hello', '=^20')   # 居中并填充 "=======hello========"
format('hello', '=>20')   # 不足位，左填充 ===============hello
format('hello', '=<20')   # 不足位，右填充 "hello==============="
```

## 进制转换
```python
x = 1234
bin(x)  # 0b10011010010
oct(x)  # 0o2322
hex(x)  # 0x4d2
# 如果你不想输出0b, 0o, 0x的话,可以使用format
format(x, 'b')  # 10011010010
format(x, 'o')  # 2322
format(x, 'x')  # 4d2
```