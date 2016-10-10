---
layout: post

title: WireShark使用笔记

tags:  [Web]

categories: [Web]

author: yddchsc

excerpt: "WireShark使用笔记"

---
##Wireshark使用笔记##

###主要特性###

+ 捕捉多种网络接口（包括无线局域网接口）
+ 支持多种其它程序捕捉的文件
+ 支持多格式输出
+ 对多种协议解码提供支持
+ 开源软件
+ Wireshark不能提供的功能：
 + 不能检测入侵，但能够查看网络的情况。
 + 不能对网络进行修改数据等操作，只能监测。
 
###主窗口快捷键###
+ Tab,Shift+Tab 在两个项目间移动，例如从一个包列表移动到下一个
+ Down 移动到下一个包或者下一个详情
+ Up 移动到上一个包或者上一个详情
+ Ctrl-Down,F8 移动到下一个包，即使焦点不在Packet list 面版
+ Ctrl-UP,F7 移动到前一个报，即使焦点不在 Packet list 面版
+ Left 在 Pactect Detail 面版，关闭被选择的详情树状分支。如果以关闭，则返回到父分支。
+ Right 在 Packet Detail 面版，打开被选择的树状分支.
+ Backspace 在Packet Detail 面版，返回到被选择的节点的父节点
+ Return,Enter 在Packet Detail 面版，固定被选择树项目。


