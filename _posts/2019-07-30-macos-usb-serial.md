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

{% highlight text %}
    苹果Logo -> 关于本机 -> 系统报告 -> 硬件 -> USB -> USB-Serial Controller
{% endhighlight %}


{% highlight text %}
 	制造商：	Prolific Technology Inc.
{% endhighlight %}

2. 安装驱动

    [Prolific驱动下载页面](http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=229&pcid=41)
    
    但是1.6.2的下载不了，我下载的是1.5.1
    
    装完以后发现还是找不到串口，通过`kextutil`命令发现`ProlificUsbSerial`根本没加载成功，因为`System Integrity Protection`
    
    {% highlight text %}
        sudo kextutil -v /System/Library/Extensions/ProlificUsbSerial.kext
    {% endhighlight %}
    
    犹豫了一下还是选择关闭`System Integrity Protection`
    
    具体操作可以参考 [How to turn off System Integrity Protection in macOS](https://www.imore.com/how-turn-system-integrity-protection-macos)

3. 连接串口工具
    - CoolTerm，[下载页面](https://freeware.the-meiers.org/)，不推荐
        
        这个工具不太好用，删除键有问题，经常出现`..`，而且没有颜色（或者我没找到在哪配置）
        
    - screen，系统自带，推荐
        
        配合iterm2很好用
        
        连接串口命令（`tty.usbserial`和`115200`根据实际情况替换）
        
        {% highlight text %}
        screen /dev/tty.usbserial 115200
        {% endhighlight %}

        
        但是存在一个问题，就是直接关闭窗口后再连接就会报错，因为窗口关闭时并没有释放与串口的连接，我的解决办法是：
        
        先用`screen -ls`查看打开的会话
        
        {% highlight text %}
        screen -ls
        {% endhighlight %}
        
        然后结束会话（`ttys003.MacBook-Pro`是会话名称，要替换成通过`ls`命令列出的会话名称）
        
        {% highlight text %}
        screen -X -S ttys003.MacBook-Pro quit
        {% endhighlight %}
        
        这样就可以再连接了


    