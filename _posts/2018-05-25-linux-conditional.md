正在学习 Linux，记录一下学习历程。

希望在一条命令执行成功的条件下执行另一条命令：

```
command1 && command2
```

希望在一条命令执行失败的条件下执行另一条命令：

```
command1 || command2
```

通常在 shell 脚本中使用。

```
grep Harley people > /dev/null && sort people > outputfile
```

如果文件 people 中包含 Harley，那么不会在屏幕上有任何显示，并且执行 sort people > outputfile

```
update || echo "update fail"
```

如果 update 命令失败，那么显示 update fail
