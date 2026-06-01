---
title: "［python 音频控制］"
date: 2022-07-25
categories: [Python]
tags:
  - python 音频控制
description: pyaudio 控制指定设备，录制音频/采集音频流/播放音频 !/usr/bin/env python3 -- coding:utf-8 -- ------------- 音频设备操作模块 ------------------
---
> 本文属于 **Python 专题** 系列，记录学习与工作中的实践笔记。

## 📌 简介

pyaudio 控制指定设备，录制音频/采集音频流/播放音频 !/usr/bin/env python3 -- coding:utf-8 -- ------------- 音频设备操作模块 ------------------

---

### pyaudio 控制指定设备，录制音频/采集音频流/播放音频
```python
#!/usr/bin/env python3
#-*- coding:utf-8 -*-
#------------- 音频设备操作模块 -------------------
#
#   功能:   录制/获取音频流/播放音频
#   时间：  2021-09-13
#
#--------------------------------------------------

import sys ,pyaudio, wave
from tqdm import tqdm

class UacAudioInAndOut:
    def __init__(self):
        """
            功能:   录音参数初始化
                    创建vad检测模块对象
            参数:   /
            返回值: /
        """
        self.input_format_dict = {"S8_LE":16, "S16_LE":8, "S24_LE":4, "S32_LE":2}
        self.framerate_list = [8000, 11025, 16000, 22050, 32000, 44100, 48000,
                            88200, 96000, 176400, 192000, 352800, 384000]
```python
def _inforPrintf(self, infor_content):
"""
功能:   检测操作系统，使用正确编码
输出打印信息
参数:   infor_content: 信息内容
返回值: /
"""
if sys.platform != "linux" and sys.platform != "darwin":
infor_content = str(infor_content).encode("gbk","ignore").decode("gbk")
print(infor_content)

def GetAllDevInfor(self):
"""
功能:   显示支持设备信息
参数:   /
返回值: /
"""
PA = pyaudio.PyAudio()
self._inforPrintf("----------------------< 本机支持设备 >------------------------------")
for dev_index in range(PA.get_device_count()):
self._inforPrintf("\n-------------------------------------------------------")
for key in PA.get_device_info_by_index(dev_index):
self._inforPrintf("%s:%s"%(key, str(PA.get_device_info_by_index(dev_index)[key])))
self._inforPrintf("========================================================")

def GetUacDevInfor(self, devKeywordOrIndex=None):
"""
功能:   获取UAC设备信息
参数:   devKeywordOrIndex: 设备名称关键字或索引
返回值: dic 设备信息字典
False 设备信息获取失败
"""
PA = pyaudio.PyAudio()
if devKeywordOrIndex == None:
self._inforPrintf("\033[0;36;31m[UacAudioInAndOut] 未设设备, 当前使用默认设备\033[0m")
return PA.get_default_input_device_info()
if str(devKeywordOrIndex).isdigit():
devKeywordOrIndex = int(devKeywordOrIndex)
return PA.get_device_info_by_index(devKeywordOrIndex)

uac_infor_list = []
for uac_index in range(PA.get_device_count()):
if PA.get_device_info_by_index(uac_index).get("name").find(str(devKeywordOrIndex)) >= 0:
uac_infor_list.append(PA.get_device_info_by_index(uac_index))

if len(uac_infor_list) > 1:
self._inforPrintf("\033[0;36;33m[UacAudioInAndOut] UAC 设备有多个，\
请修正关键字, 当前设备如下: %s\033[0m"%str(uac_infor_list))
return False
else:
return uac_infor_list.pop()

def is_framerate_supported(self, setFramerate, UacAudioInHandle,
load_parame_dict, input_or_output="input"):
"""
功能:   判断当配置在指定设备中是否支持
参数:   setFramerate:       设置采样率
UacAudioInHandle:   设备句柄
load_parame_dict:   加载字典
input_or_output:    输入/输出功能
返回值: bool True/False
"""
try:
if input_or_output == "input":
UacAudioInHandle.is_format_supported(rate=float(setFramerate),
input_device=load_parame_dict['index'],
input_channels=load_parame_dict['setInputChannels'],
input_format=load_parame_dict['_setInputFormat'])
else:
UacAudioInHandle.is_format_supported(rate=float(setFramerate),
output_device=load_parame_dict['index'],
output_channels=load_parame_dict['maxOutputChannels'],
output_format=UacAudioInHandle.get_format_from_width(load_parame_dict['setOutputFormat']))
return True
except:
return False

def LoadUacAudioInDevice(self, maxStreamDuration=1000, setInputChannels=None,
setInputFormat=None, devKeywordOrIndex=None):
"""
功能:   加载音频获取设备
参数:   maxStreamDuration=1000 默认一段流时长
setInputChannels:           通道数
setInputFormat:             位宽
devKeywordOrIndex:    录音设备关键字/索引
返回值:
成功: UacAudioInHandle, StreamHandle, load_parame_dict
失败: False
"""
try:
load_parame_dict = {}
uac_infor_dict = self.GetUacDevInfor(devKeywordOrIndex)
if not setInputFormat:
_Format = "S16_LE"
self._inforPrintf("\033[0;36;33m[UacAudioInAndOut] 未设置位宽，使用默认 S16_LE \033[0m")
else:
_Format = setInputFormat
setInputFormat = self.input_format_dict[_Format]

if not setInputChannels or int(setInputChannels) > uac_infor_dict["maxInputChannels"]:
setInputChannels = uac_infor_dict["maxInputChannels"]
self._inforPrintf("\033[0;36;33m[UacAudioInAndOut] 输入通道未设置/超出当前设备最大值,使用默认最大通道 %s\
\033[0m"%setInputChannels)
else:
setInputChannels = int(setInputChannels)
dev_index = uac_infor_dict["index"]
load_parame_dict["index"]=dev_index
load_parame_dict["setInputFormat"] = _Format
load_parame_dict["_setInputFormat"] = setInputFormat
load_parame_dict["setInputChannels"] = setInputChannels
UacAudioInHandle = pyaudio.PyAudio()
for setInputFramerate in self.framerate_list:
if self.is_framerate_supported(setInputFramerate, UacAudioInHandle, load_parame_dict):
load_parame_dict["setInputFramerate"] = setInputFramerate
break
#计算数据大小一段
CHUNK_SIZE = int(setInputFramerate * maxStreamDuration / 1000)
load_parame_dict["CHUNK_SIZE"] = CHUNK_SIZE
self._inforPrintf("\033[0;36;38m[UacAudioInAndOut] 加载参数: %s\033[0m"%str(load_parame_dict))
#加载设备
StreamHandle = UacAudioInHandle.open(
format=load_parame_dict['_setInputFormat'],
channels=load_parame_dict['setInputChannels'],
rate=load_parame_dict['setInputFramerate'],
input=True,
input_device_index=load_parame_dict['index'],
start=False,
frames_per_buffer=int(CHUNK_SIZE))
#开始流获取
StreamHandle.start_stream()
return UacAudioInHandle, StreamHandle, load_parame_dict
except:
self._inforPrintf("\033[0;36;31m[UacAudioInAndOut] Uac AudioIn 加载失败\033[0m")
return False, False, False

def LoadUacAudioOutDevice(self, devKeywordOrIndex):
"""
功能:   加载音频输出设备
参数:   /
返回值: UacAudioInHandle 或 False
"""
try:
uac_infor_dict = self.GetUacDevInfor(devKeywordOrIndex)
UacAudioInHandle = pyaudio.PyAudio()
return UacAudioInHandle, uac_infor_dict
except:
return False

def GetUacAudioInStream(self, StreamHandle, CHUNK_SIZE):
"""
功能:   开始采集声卡音频
生成音频流
参数:   UacAudioInHandle:   设备句柄
StreamHandle:       流句柄
返回值  chunk_data 流数据
"""
return StreamHandle.read(CHUNK_SIZE, exception_on_overflow=False) #防止溢出

def UacAudioOutPlay(self, playWavFile, Repeat=None, Pdict=None, devKeywordOrIndex=None,):
"""
功能:   可以循环播放指定文件
参数:   playWavFile:            播放文件路径
Repeat:                 循环播放次数
CustomizeAudioParam:    自定义播放参数
返回值: /
"""
UacAudioInHandle, uac_infor_dict = self.LoadUacAudioOutDevice(devKeywordOrIndex)
self._inforPrintf(str(uac_infor_dict).encode("gbk","ignore").decode("gbk"))
self._inforPrintf("\033[1;36;34m[UacAudioInAndOut] 指定设备: %s\t播放文件: %s\t循环总数: %s\
\033[0m"%(devKeywordOrIndex, playWavFile,Repeat))
try:
chunk=1024
pfb = wave.open(playWavFile, 'rb')
setOutputFormat = pfb.getsampwidth()
setOutputChannels = pfb.getnchannels()
setOutputFramerate = pfb.getframerate()
uac_infor_dict['setOutputFormat'] = setOutputFormat

if setOutputChannels > uac_infor_dict["maxOutputChannels"]:
self._inforPrintf("\033[0;36;31m[UacAudioInAndOut] 当前通道数，在该设备上不支持, \
设备最大通道数: %s\033[0m"%uac_infor_dict["maxOutputChannels"])
return False
if not self.is_framerate_supported(setOutputFramerate, UacAudioInHandle, uac_infor_dict, "output"):
self._inforPrintf("\033[0;36;31m[UacAudioInAndOut] 当前文件采样率，在该设备上不支持,\
设备默认采样率: %s\033[0m"%uac_infor_dict["defaultSampleRate"])
return False
else:
uac_infor_dict["defaultSampleRate"] = setOutputFramerate
stream = UacAudioInHandle.open(
output_device_index=uac_infor_dict['index'],
format=UacAudioInHandle.get_format_from_width(setOutputFormat),
channels=setOutputChannels,
rate=setOutputFramerate,
output=True)

if Repeat == "Dead_cycle":
self._inforPrintf("\033[1;36;33m[UacAudioInAndOut] Dead cycle play !!! \033[0m")
while True:
if type(Pdict) == dict and Pdict["play status"] == "stop":
break
pfb = wave.open(playWavFile, 'rb')
while True:
data = pfb.readframes(chunk)
if not data:
break
stream.write(data)
else:
for index in tqdm(range(int(Repeat))):
if type(Pdict) == dict and Pdict["play status"] == "stop":
break
pfb = wave.open(playWavFile, 'rb')
while True:
data = pfb.readframes(chunk)
if not data:
break
stream.write(data)

stream.stop_stream()
stream.close()
self.CloseAudioDevice(UacAudioInHandle)
return True
except:
stream.stop_stream()
stream.close()
return False

def UacAudioInRecord(self, saveWavFile, recordTime, #单位秒
setInputChannels=None,
setInputFormat=None,
devKeywordOrIndex=None):
"""
功能:   录制音频文件
参数:   recordTime:         录音时长, 单位(s)
setInputFramerate:  采样率
setInputChannels:   通道数
setInputFormat:     位宽
devKeywordOrIndex:      录音设备索引
返回值: /
"""
maxStreamDuration=1000
load_parame_dict = {}
UacAudioInHandle, StreamHandle, load_parame_dict = self.LoadUacAudioInDevice(
maxStreamDuration,
setInputChannels,
setInputFormat,
devKeywordOrIndex)
if not UacAudioInHandle or not StreamHandle:
self._inforPrintf("\033[0;36;31m[UacAudioInAndOut] 录音失败\033[0m")
return False

self._inforPrintf("\033[1;36;34m[UacAudioInAndOut] 录音 -> 文件名: %s 时长: %s\
\033[0m"%(saveWavFile,recordTime))
self._inforPrintf(load_parame_dict["CHUNK_SIZE"])
data_list = []
for recordTime_index in range(int(recordTime)):
data = None
data = StreamHandle.read(load_parame_dict["CHUNK_SIZE"], exception_on_overflow=False)
data_list.append(data)
StreamHandle.stop_stream()
StreamHandle.close()
self.CloseAudioDevice(UacAudioInHandle)
with wave.open(saveWavFile, "wb") as wavfb:
wavfb.setnchannels(load_parame_dict["setInputChannels"])
wavfb.setsampwidth(UacAudioInHandle.get_sample_size(load_parame_dict["_setInputFormat"]))
wavfb.setframerate(load_parame_dict["setInputFramerate"])
wavfb.writeframes(b''.join(data_list))

"""
功能:   关闭音频流设备
参数:   UacAudioInHandle
返回值: bool True/False
"""
try:
StreamHandle.stop_stream()
StreamHandle.close()
self.CloseAudioDevice()
return True
except:
return False

def CloseAudioDevice(self, UacAudioDeviceHandle):
"""
功能:   释放 Audio 设备
参数:   UacAudioDeviceHandle
返回值: bool True/False
"""
try:
UacAudioDeviceHandle.terminate()
return True
except:
return False
```

if __name__=="__main__":
    asv = UacAudioInAndOut()
    asv.GetAllDevInfor()
    #asv.UacAudioOutPlay(sys.argv[1], int(sys.argv[2]), None, sys.argv[3])
    asv.UacAudioInRecord(sys.argv[1], sys.argv[2])
```
---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/125900632) 迁移至 GitHub Pages，并在本站持续维护。
