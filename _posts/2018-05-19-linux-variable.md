正在学习 Linux，记录一下学习历程。

## 变量

变量：储存数据的实体

种类：shell 变量和环境变量

操作：创建变量，查看变量值，修改变量值，销毁变量

环境变量对所有进程可用，通常称环境变量为全局变量。

shell 变量是创建它们的 shell 的局部变量。

## 创建变量

变量名=变量值

```
HARLEY=cool
```

显示变量
显示环境变量：

```
env
printenv
```

显示 shell 变量：

```
set
```

显示变量值：

```
echo $[变量名]
```

## 修改变量

变量名=修改值

```
HARLEY=smart
```

## 销毁变量

```
unset [变量名]
unset HARLEY
```

## shell 变量变为环境变量

```
export [变量名]

export HARLEY
```
