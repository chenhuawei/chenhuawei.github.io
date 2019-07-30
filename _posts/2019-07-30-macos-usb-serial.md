---
layout: post
title:  "macOS下连接USB串口"
date:   2019-07-30 21:51:52 +0800
categories: macOS
tags:
- macOS
- usb
- serial
---


# macOS下连接USB串口
1. 查看USB串口的供应商

    `苹果Logo -> 关于本机 -> 系统报告 -> 硬件 -> USB -> USB-Serial Controller`
    
    主要关注制造商是哪个厂的，好去下载驱动。我的是：`Prolific Technology Inc.`

2. 安装驱动

    [Prolific驱动下载页面](http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=229&pcid=41)
    
    1.6.2的下载不了，我下载的是1.5.1
    
    装完以后发现还是找不到串口，通过`kextutil`命令发现`ProlificUsbSerial`根本没加载成功，因为`System Integrity Protection`是开启的
    
    通过`kextutil`命令查看内核模块加载状态`sudo kextutil -v /System/Library/Extensions/ProlificUsbSerial.kext`
    
    最终选择关闭`System Integrity Protection`
    
    具体操作可以参考 [How to turn off System Integrity Protection in macOS](https://www.imore.com/how-turn-system-integrity-protection-macos)

3. 连接串口工具
    - CoolTerm，[下载页面](https://freeware.the-meiers.org/)，不推荐
        
        这个工具不太好用，删除键有问题，经常出现`..`，而且没有颜色（或者我没找到在哪配置）
        
    - screen，系统自带，推荐
                
        连接串口命令`screen /dev/tty.usbserial 115200`（根据实际情况替换`tty.usbserial`和`115200`）
        
        但是存在一个问题，就是直接关闭窗口后再连接就会报错，因为窗口关闭时并没有释放与串口的连接，我的解决办法是：
        
        先用`screen -ls`查看打开的会话
                
        然后通过命令`screen -X -S ttys003.MacBook-Pro quit`结束会话（`ttys003.MacBook-Pro`是会话名称，要替换成通过`screen -ls`命令列出的会话名称）
        
        这样就可以再连接了


    