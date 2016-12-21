1.CentOS安装tmux
```
yum install tmux
```

2.常用命令
```
tmux #启动tmux

tmux ls #显示已有tmux列表

tmux attach <session_name> 数字 #选择tmux

# tmux常用命令前缀
Ctrl+b 

#以下命令用prefix代替Ctrl+b
prefix + %  #横向分割窗口

prefix + "  #纵向分割窗口

prefix + b q  #显示窗口号，键入窗口号可直接跳转至相应窗口

prefix + n  #切换到下一个窗口

prefix + p  #切换到上一个窗口

prefix + b o  #窗口切换

prefix d  #临时断开session

```
目前常用的就是这些命令，当然如果对配置文件进行编写的话，更够更大程度上的自定义这个插件。