---
title: 右键菜单VSCode打开文件夹
tags: vscode
categories: 
- 其他
- vscode使用
---
VSCode在安装时会有右键快捷打开的选项，但是默认是不勾选的，所有很多人会因此无法使用右键快捷打开菜单，下面提供在安装完成后如何手动添加右键快捷打开的方法：

### 添加右键打开文件

1. Win+R 打开运行，输入regedit，打开注册表，找到HKEY_CLASSES_ROOT\*\shell分支，如果没有shell分支，则在*下点击右键，选择“新建－项”，建立shell分支。
2. 在shell下新建“VisualCode”项，在右侧窗口的“默认”键值栏内输入“Open as vscode project”，这是右键上显示值，也就是文字。其事可以随便写，只是为了方便记忆和分辨。
3. 在“VisualCode”下再新建Command项，在右侧窗口的“默认”键值栏内输入程序所在的安装路径，我的是："D:\Program Files (x86)\VSCode\code.exe" "%1"。其中的%1表示要打开的文件参数。
4. 关闭注册表，即可生效。

### 添加右键打开文件夹
以上方法可以在选中文件时右键在菜单栏中显示："用VSCode打开文件夹"，但当右键文件夹时仍然不能显示此选项，所以还要进行下面的操作：

打开注册表，找到HKEY_CLASSES_ROOT\Directory\shell，按照上面2、3的方法添加即可。

### 添加Icon，也就是文字前面的图标
在原有的VisualCode项上新建可扩充字符串值，命名为Icon，像一个键值对那样把"D:\Program Files (x86)\VSCode\code.exe"放进去就可以了。