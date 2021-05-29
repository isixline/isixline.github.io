正在学习Linux，记录一下学习历程。


这里讨论的是shell选项，控制shell的行为，如shell是交互式还是非交互式的。（并不是命令的选项）

shell选项就像on/off开关一样。

当打开一个选项时，就是设置了这个选项，相当于告诉shell以某种方式运行。

当关闭一个选项时，就算复位了这个选项，相当于告诉shell停止以这种方式运行。

ps：对于Bourne shell家族来说，用shell选项控制shell行为。对于C-Shell家族来说，通过shell变量控制。


| 常用选项 | | |
| - | - | - |
| -I | ignoreeof |	忽略eof信号^D,使用exit退出shell |
| -m | monitor | 启用作业控制 |
| -C | noclobber | 防止重定向标准输出时偶然移除文件 |
| -V | vi | 命令行编辑器：vi模式 |
| -E | emacs | 命令行编辑器：Emacs模式 |

显示shell选项
```
set -o
```

设置shell选项
```
set -o [选项名]
```

复位shell选项
```
set +o [选项名]
```


ps：人们将常见的-o用于设置，而不常见的+o用于复位。