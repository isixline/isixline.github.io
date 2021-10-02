## GHC
ghc是Haskell的编译器，类似于gcc与C的关系。
ghc可以对Haskell脚本（后缀名通常是.hs）进行编译

## GHCI
ghci是Haskell的交互命令行

## Stack
Stack是Haskell工具栈，是Haskell程序的项目管理工具。
能够完成包管理，工程编译，运行，测试管理。
#### 安装stack
```
curl -sSL https://get.haskellstack.org/ | sh
```
#### 初始化stack
```
stack setup
```
#### 使用ghci
```
stack ghci
```
#### 创建新项目
```
stack new [project-name]
```


