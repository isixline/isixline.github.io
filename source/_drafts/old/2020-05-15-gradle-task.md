---
layout: post
title: "gradle task"
categories: gradle
---

## 创建 Task

```
task hello {
    doLast {
        println 'Hello world!'
    }
}

```

或者

```
task hello << {
    println 'Hello world!'
}
```

执行 task hello。

## 执行Task

```
gradle -q hello
```

## 动态创建 Task

```
4.times { counter ->
    task "task$counter" << {
        println "I'm task number $counter"
    }
}
```

## 默认任务

```
defaultTasks 'clean', 'run'
```

## 钩子函数

```
gradle.taskGraph.whenReady {taskGraph ->
    if (taskGraph.hasTask(release)) {
        version = '1.0'
    } else {
        version = '1.0-SNAPSHOT'
    }
}
```

PS：
-q. 代表 quiet 模式. 它不会生成 Gradle 的日志信息 (log messages), 所以用户只能看到 tasks 的输出. 它使得的输出更加清晰.