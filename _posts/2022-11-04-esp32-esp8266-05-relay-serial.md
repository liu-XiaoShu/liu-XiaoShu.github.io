---
title: "[ESP32/ESP8266专题笔记-5] ESP8266开发板-Ｍicropython-串口控制继电器"
date: 2022-11-04
categories: [嵌入式]
tags:
  - ESP32/ESP8266专题笔记
description: ESP8266开发板-Ｍicropython-串口控制继电器 硬件篇 1 开发板 ![在这里插入图片描述](
---

### ESP8266开发板-Ｍicropython-串口控制继电器

#### 硬件篇

###### 1 开发板

![在这里插入图片描述](/assets/img/posts/esp32-esp8266-05-relay-serial/img-01.png)

###### 2 接口说明

物力接口| 继电器定义  
---|---  
GPIO14| 继电器1#  
GPIO15| 继电器2#  
GPIO5| 继电器3#  
GPIO4| 继电器4#  
  
#### 软件篇

##### 1 编写ESP8266开发板-Ｍicropython 代码
    
    
    #-*- conding:utf-8 -*-
    #-------------------------- 继电器控制板-代码 ---------------------------
    #
    #   控制板:     pyWIFI-ESP8266 + 继电器板
    #   代码:       MicroPython
    #   通讯方式:   串口
    #   Author:     liu-XiaoShu
    #   Date:       2020-10-15
    #   说明:       通过串口命令控制ESP8266的GPIO电平输出，从而控制继电器
    #               RelayControl.py
    #---------------------------------------------------------------------------
    import time
    import uos
    from machine import UART, Pin
    
    class Esp8266RelayControl:
        def __init__(self):
            self.system_infor = "="*100 + \
                                "\t\nWelcome to ESP8266 relay control board ..." +\
                                "\t\nAuthor:    liu-XiaoShu" + \
                                "\t\nDate:      2020-10-15" + \
                                "\t\nVersion:   V1.1" + \
                                "\t\nfunction:  Relay control board\n" + "~"*100
            print(self.system_infor)
            self.cmd_infor = "Relay control cmd: \n\t1. setpower [<off/on>] [relay ID]\n\t2. getpower [<relay ID>]" + \
                            "\nex:\n\tsetpower on 1\n\tgetpower 1\n\n" + \
                            "instructions:\n" + \
                            "\t 1. The LED flashes to indicate that it is starting\n" + \
                            "\t 2. After the LED flashing, press the key to enter the debugging mode within 4" + \
                            " seconds, otherwise enter the serial port control mode\n" + \
                            "\t 3. LED is always on, enter debugging mode, LED is off and enter serial port control mode\n" + \
                            "\t 4. RST key, reset the program, restart\n" + \
                            "\t 5. At present, it supports 4 relay switching\n"
            print(self.cmd_infor+ "="*100)
            self.system_infor = ""
            self.cmd_infor = ""
            self.relay_state_dict = {"1":"power off", "2":"power off", "3":"power off", "4":"power off"}
            #pin GPIO引脚配置
            self.relay_pin_dict = {"4":4, "3":5, "2":15, "1":14}
            self.power_led_pin = 2
            self.key_pin = 0
            #选择REPL/UART模式超时,超时未选择就默认UART 方便使用脚本
            self.set_REPL_UART_model_timeout = 4
            self.KEY=Pin(self.key_pin, Pin.IN, Pin.PULL_UP)
    
        def REPL_UART_separation(self):
            """
                功能:   分离REPL和串口，因串口占用使用串
                        口需要分离, 后续使用REPL 可以使用
                        webREPL
                参数:   无
                返回值: BOOL True/False
            """
            try:
                uos.dupterm(None, 1)
                print("REPL UART separation success ...")
                return True
            except:
                print("REPL UART failed to separation ...")
                return False
    
        def REPL_UART_connect(self):
            """
                功能:   连接REPL和串口
                参数:   无
                返回值: BOOL True/False
            """
            try:
                uart = UART(0, 115200)
                uos.dupterm(uart, 1)
                print("REPL UART connect success ...")
                return True
            except:
                print("REPL UART connect failed ...")
                return False
    
        def power_on_lamp_effect(self):
            """
                功能:   系统开机灯效
                参数:   无
                返回值: 无
            """
            #构建led对象，GPIO2,输出
            power_led=Pin(self.power_led_pin, Pin.OUT)
            for index in range(4):
                power_led.value(0) #点亮LED
                time.sleep_ms((index+1)*70)
                power_led.value(1) #熄灭LED
                time.sleep_ms((index+1)*50)
                power_led.value(0) #点亮LED
    
        def start_relay_control(self, receiveCmd):
            """
                功能:   解析命令，操作GPIO控制继电器
                参数：  receiveCmd: 接收到的命令
                返回值: str 命令执行结果
            """
            relayID = receiveCmd.split(" ")[-1]
            print("[relayID]: " + relayID)
            control = receiveCmd.split(" " + relayID)[0]
            print("[relay Cmd]: " + control)
            if relayID not in self.relay_pin_dict:
                return "relay ID error"
            if control == "setpower on":
                relay = Pin(self.relay_pin_dict[relayID])
                relay.value(1) #GPIO拉高
                self.relay_state_dict[relayID] = "power on"
                return "success"
            elif control == "setpower off":
                relay = Pin(self.relay_pin_dict[relayID])
                relay.value(0) #GPIO拉低
                self.relay_state_dict[relayID] = "power off"
                return "success"
            elif control == "getpower":
                return str(relayID) + " " + str(self.relay_state_dict[relayID])
            else:
                return "relay control cmd error"
    
        def start_uart_model(self):
            """
                功能:   串口模式,通过串口脚
                        本发送命令方式控制
                参数:   无
                返回值: 无
            """
            #uart 模式 led 灭
            power_led=Pin(self.power_led_pin, Pin.OUT)
            power_led.value(1)
            self.REPL_UART_separation()
            uart = UART(0, baudrate=115200)
            uart.write("Welcome to use UART model ...")
            while True:
                result = ""
                if uart.any():
                    data = ""
                    data = uart.readline()
                    data = data.decode().split("\n")[0]
                    #print(data)
                    result = self.start_relay_control(data)
                    uart.write(result)
                else:
                    time.sleep(0.5)#解决板子突然重启问题
    
        def start_repl_model(self):
            """
                功能:   REPL模式,通过minicom
                        敲命令方式控制
                参数:   无
                返回值: 无
            """
            print("Welcome to use REPL model ...")
            #EPRL 模式 led 亮
            power_led=Pin(self.power_led_pin, Pin.OUT)
            power_led.value(0)
            while True:
                result = ""
                cmd = input("cmd >>:")
                result = self.start_relay_control(cmd)
                print(result)
    
        def start_run(self):
            """
                功能:   系统入口
                参数:   无
                返回值: 无
            """
            #初始化引脚输出
            for key in self.relay_pin_dict:
                Pin(self.relay_pin_dict[key], Pin.OUT)
            #开机灯效
            self.power_on_lamp_effect()
            timeout_count = 0
            print("Please select work mode ....")
            while timeout_count < self.set_REPL_UART_model_timeout:
                print("Timeout: " + str(timeout_count) + "s")
                if self.KEY.value()==0:   #按键被按下
                    time.sleep_ms(10) #消除抖动
                    if self.KEY.value()==0: #确认按键被按下
                        #进入调试/REPL模式
                        self.start_repl_model()
                        while not self.KEY.value():
                            pass
                timeout_count += 1
                time.sleep(1)
            #进入串口模式,方便脚本控制
            self.start_uart_model()
    
    if __name__=="__main__":
        ERC = Esp8266RelayControl()
        ERC.start_run()
    
    

###### 2 代码文件名设置为 main.py 使其上电自运行

###### 3 烧入代码
    
    
    sudo ampy --port /dev/ttyUSB0 put main.py
    

###### 4 上电启动，可以通过串口发送指令，控制板子输出对应电平，从而控制继电器等

###### 6 运行查看
    
    
    Started webrepl in normal mode
    ====================================================================================================	
    Welcome to ESP8266 relay control board ...	
    Author:    liu-XiaoShu	
    Date:      2021-03-19	
    Version:   V1.1	
    function:  Relay control board
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    Relay control cmd: 
    	1. setpower [<off/on>] [relay ID]
    	2. getpower [<relay ID>]
    ex:
    	setpower on 1
    	getpower 1
    
    instructions:
    	 1. The LED flashes to indicate that it is starting
    	 2. After the LED flashing, press the key to enter the debugging mode within 4 seconds, otherwise enter the serial port control mode
    	 3. LED is always on, enter debugging mode, LED is off and enter serial port control mode
    	 4. RST key, reset the program, restart
    	 5. At present, it supports 4 relay switching
    ====================================================================================================
    Please select work mode ....
    Timeout: 0s
    Timeout: 1s
    Timeout: 2s
    Timeout: 3s
    Welcome to use REPL model ...
    cmd >>:set power on 1
    [relayID]: 1
    [relay Cmd]: set power on
    relay control cmd error
    cmd >>:setpower on 1
    [relayID]: 1
    [relay Cmd]: setpower on
    success
    cmd >>:getpower 1
    [relayID]: 1
    [relay Cmd]: getpower
    1 power on
    cmd >>:setpower off 1
    [relayID]: 1
    [relay Cmd]: setpower off
    success
    cmd >>:getpower 1
    [relayID]: 1
    [relay Cmd]: getpower
    1 power off
    cmd >>:

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/123504646) 迁移至本站。
