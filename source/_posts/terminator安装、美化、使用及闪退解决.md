---
title: terminator安装、美化、使用及闪退解决
index_img: /img/article/terminal.jpg
categories: 
- Linux
tags:
- Linux
- terminal
date: 2020-03-29 10:30:00
---

选择 terminator 主要是看中了它优秀的分屏功能。



### 一、安装 terminator

1.  添加仓库/软件源

```bash
sudo add-apt-repository ppa:gnome-terminator
```
2. 更新源

```bash
sudo apt update
```
3. 安装 terminator

```bash
sudo apt install terminator
```

### 二、美化 terminator 界面
这是初始界面。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019121322374167.png)
通过修改配置文件美化窗口。
在终端输入以下命令：

```bash
cd ~/.config/terminator/ 
sudo gedit config
```
注意！第一次进入~/.config/terminator/目录时，config文件是不存在的，要通过以下步骤找到config
1. 在 terminator 黑色背景上右键
2. 单击 Preferences (首选项)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019121322541367.png)
3. 选择 Profiles (布局)，按照图中标记选择，default 处可以自定义名称。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191213225543183.png)
4. 按照上面的步骤找到 config，进行编辑。

我的 config 内容：
```bash
[global_config]
  enabled_plugins = CustomCommandsMenu, LaunchpadCodeURLHandler, APTURLHandler, LaunchpadBugURLHandler
  handle_size = -3
  inactive_color_offset = 1.0
  suppress_multiple_term_dialog = True
  title_transmit_bg_color = "#3e3838"
  title_transmit_fg_color = "#000000"
[keybindings]
[layouts]
  [[default]]
    [[[child1]]]
      parent = window0
      profile = default
      type = Terminal
    [[[window0]]]
      parent = ""
      size = 925, 570
      type = Window
[plugins]
[profiles]
  [[default]]
    background_color = "#1e1e1e"
    background_darkness = 0.8
    background_image = None
    background_type = transparent
    cursor_color = "#e8e8e8"
    cursor_shape = ibeam
    font = Ubuntu Mono 14
    foreground_color = "#e8e8e8"
    palette = "#292424:#5a8e1c:#2d5f5f:#cdcd00:#1e90ff:#cd00cd:#00cdcd:#d6d9d4:#4c4c4c:#868e09:#00ff00:#ffff00:#4682b4:#ff00ff:#00ffff:#ffffff"
    scroll_background = False
    scrollback_lines = 3000
    show_titlebar = False
    use_system_font = False
```
美化后的 terminator 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191213231047704.png)
### 三、常用快捷键
<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>O</kbd> 水平分割终端（分成上下两个窗口）
<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>E</kbd> 垂直分割终端（分成左右两个窗口）
<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>W</kbd> 关闭当前终端
<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>X</kbd>  放大（还原）当前终端
<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>G</kbd> 清屏
<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>Right</kbd>/<kbd>Left</kbd> 在垂直分割的终端中将分割条向右/左移动
<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>S</kbd> 隐藏/显示滚动条
<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>Q</kbd> 关闭所有终端（退出程序）
<kbd>F11</kbd> 全屏

### 四、打不开或闪退问题
一般发生这种问题的电脑都是默认 Python3.x 版本的，而 terminator 是基于python2开发的，所以会出问题。
如下方法解决：
终端输入：
```bash
sudo gedit /usr/share/terminator/terminator
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191213233134522.png)
将第一行的将第一行的 ```#!/usr/bin/python ```修改为 ```#!/usr/bin/python2```
然后 terminator 就能打开了，亲测可用。

