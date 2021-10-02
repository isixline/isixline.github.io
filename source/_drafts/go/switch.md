## switch
```go
switch coinflip() {
case "heads":
    heads++
case "tails":
    tails++
default:
    fmt.Println("landed on edge!")
}
```
不需要显式地在每一个case后写break，语言默认执行完case后的逻辑语句会自动退出。
当然了，如果你想要相邻的几个case都执行同一逻辑的话，需要自己显式地写上一个fallthrough语句来覆盖这种默认行为。
## 无tag switch(tagless switch)
```go
func Signum(x int) int {
    switch {
    case x > 0:
        return +1
    default:
        return 0
    case x < 0:
        return -1
    }
}
```
和switch true是等价的,然后将每个case的表达式和true值进行比较
可以直接罗列多种条件，像其它语言里面的多个if else一样。
像for和if控制语句一样，switch也可以紧跟一个简短的变量声明，一个自增表达式、赋值语句，或者一个函数调用(译注：比其它语言丰富)。
