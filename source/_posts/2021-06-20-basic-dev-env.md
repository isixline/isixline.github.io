---
title: 基础环境配置
date: 2021-06-20 00:00
---

## 安装Homebrew 
https://brew.sh/index_zh-cn

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## jdk 安装
jdk1.8下载地址
https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

查看安装路径：/usr/libexec/java_home -V

默认安装路径：/Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk/Contents/Home

进入 ～目录

编辑.bash_profile，加入以下内容
```
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk/Contents/Home

PATH=$JAVA\_HOME/bin:$PATH:.

CLASSPATH=$JAVA\_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.

export JAVA_HOME

export PATH

export CLASSPATH
```
执行 source ~/.bash_profile  使配置生效。

查看 java 版本：java -version

## gradle 安装
#### 使用 brew 安装 gradle
```
brew install gradle
```

#### 查看 gradle 版本
```
gradle -v
```

## git 安装
#### 使用 brew 安装 git
```
brew install git
```

#### 配置用户信息
```
git config --global user.name yourName
git config --global user.email yourEmail
```

#### 配置ssh
查看是否有ssh配置
```
cd ~/.ssh
```

生成ssh
```
ssh-keygen -t rsa -C "yourEmail"
```
会在 .ssh 下生成 id_rsa 和 id_rsa.pub

将 id_rsa.pub 中的内容配置在github中

## docker 安装
#### 使用 brew 安装
```
brew install --cask docker
```

#### 下载安装
https://hub.docker.com/editions/community/docker-ce-desktop-mac

## 使用zsh
安装 oh-my-zsh
```
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

目录下没有.zshrc
```
touch .zshrc
cp ~/.zshrc   ~/.zshrc.orig
```

创建zsh配置文件
```
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

配置兼容bash环境变量
```
.zshrc 文件中添加source ~/.bash_profile
```

修改默认shell
```
chsh -s /bin/zsh
```
修改zsh主题
.zshrc 文件 添加 ZSH_THEME=gnzh

