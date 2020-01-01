---
layout: mypost
title: 三种用Python检测COM Port端口的方法
categories: [Python]
---

## 使用pyserial库自带的tools.list_ports
1.  安装pyserial库
    ```
    pip install pyserial
    ```
2.  使用Python查询的实例
    ```python
    import serial.tools.list_ports
    ports = serial.tools.list_ports.comports()
    
    for port, desc, hardware_id in sorted(ports):
            print("{}: {} [{}]".format(port, desc, hardware_id))
    
    # COM1: 通信端口 (COM1) [ACPI\PNP0501\0]
    # COM4: Quectel USB AT Port (COM4) [USB VID:PID=2C7C:0800 SER=6 LOCATI`ON=1-9:x.2]
    # COM5: Quectel USB DM Port (COM5) [USB VID:PID=2C7C:0800 SER=6 LOCATION=1-9:x.0]
    # COM6: Quectel USB NMEA Port (COM6) [USB VID:PID=2C7C:0800 SER=6 LOCATION=1-9:x.1]
    # COM10: USB Serial Port (COM10) [USB VID:PID=0403:6001 SER=FTAS68JPA]`
    ```
3.  特别的可以直接在DOS窗口中使用
    ```
    python -m serial.tools.list_ports
    ```  

>注意：此方法可能在读取端口后，部分第三方程序调用查询到的端口可能存在端口占用的情况


## 使用winreg库查询注册表信息
1.  首先`win+R`然后输入`regedit`可以看到注册表相关信息  
2.  找到`计算机\HKEY_LOCAL_MACHINE\HARDWARE\DEVICEMAP\SERIALCOMM`路径，此路径刷新即为设备当前电脑端口的连接情况  
3.  使用Python查询的实例  
    ```python
    import winreg


    def check_port():
        port_name_list = []
        key = winreg.OpenKey(winreg.HKEY_LOCAL_MACHINE, r"HARDWARE\DEVICEMAP\SERIALCOMM")
        port_nums = winreg.QueryInfoKey(key)[1]  # 获取列表中端口的数量
        try:
            for port in range(port_nums):
                name, value, _ = winreg.EnumValue(key, port)
                port_name_list.append([name, value, type])
            return port_name_list
        except OSError:
            return port_name_list
    
    
    if __name__ == '__main__':
        print(check_port())
    
    # [['\\Device\\Serial0', 'COM1', <class 'type'>], ['\\Device\\VCP0', 'COM10', <class 'type'>], ['\\Device\\QCUSB_COM6_2', 'COM6', <class 'type'>], ['\\Device\\QCUSB_COM5_1', 'COM5', <class 'type'>], ['\\Device\\QCUSB_COM4_3', 'COM4', <class 'type'>], ['\\Device\\QCUSB_COM3_4', 'COM3', <class 'type'>]]
    ```  
  
>重要：pyserial库调用口时，可能需要调用.close()方法，端口消失后注册表中端口才会正常消失     

## 使用WMI查询端口信息
1.  WMI(Windows Management Instrumentation)，用户可以使用 WMI 管理Windows计算机
2.  WMI中`Win32_PnPEntity class`可以查询到端口的相关信息，详细信息查看[官方文档](https://docs.microsoft.com/en-us/windows/win32/cimwin32prov/win32-pnpentity)
3.  知道端口名称时候，使用DOS窗口查询端口的方法：
    ```
    wmic PATH Win32_PnPEntity WHERE Name="USB Serial Port (COM5)"
    ```
    ![DOS](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2020/01/01/1.jpg?raw=true)
    使用Python查询
    ```python
    import os

    return_value = os.popen('wmic PATH Win32_PnPEntity WHERE Name="USB Serial Port (COM10)"').read()
    print(return_value)
    ```
4.  使用python查询的实例
    1.  wmi库的安装
        ```
        pip install wmi
        ```  
          
        >调用过程中可能出现缺少win32com组件，安装好pywim32后win32com会自动安装，
        安装方法参照[安装win32com或pywin32报错](http://aoeivu.github.io/posts/2019/12/31/%E5%AE%89%E8%A3%85win32com%E6%88%96pywin32%E6%8A%A5%E9%94%99.html)  
    2. python实例  
        ```python  
        import re
        import wmi
        
        query = "SELECT * FROM Win32_PnPEntity WHERE Name LIKE '%(COM%)'"  # WQL
        com_ports = wmi.WMI().query(query)
        for com in com_ports:
            com_name = com.Name
            com_port = ''.join(re.findall(r'.*\((COM\d+)\).*', com_name))
            print([com_name, com_port])
        
        # ['Quectel USB AT Port (COM4)', 'COM4']
        # ['通信端口 (COM1)', 'COM1']
        # ['Quectel USB DM Port (COM5)', 'COM5']
        # ['USB Serial Port (COM10)', 'COM10']
        # ['Quectel USB NMEA Port (COM6)', 'COM6']
        ```  
    
>Win32_PnPEntity WMI类表示即插即用设备的属性。 即插即用实体在“控制面板”中的“设备管理器”中显示为条目。