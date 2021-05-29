## 模式匹配

模式匹配通过检查数据的特定结构来检查其是否匹配，并按模式从中取得数据。

```
factorial :: (Integral a) => a -> a
factorial 0 = 1
factorial n = n * factorial (n - 1)
```

在定义模式时，一定要留一个万能匹配的模式，这样我们的程序就不会为了不可预料的输入而崩溃。

对 Tuple 使用模式匹配

```
addVectors :: (Num a) => (a, a) -> (a, a) -> (a, a)
addVectors (x1, y1) (x2, y2) = (x1 + x2, y1 + y2)
```

对 List 使用模式匹配

```
head' :: [a] -> a
head' [] = error "Can't call head on an empty list, dummy!"
head' (x:_) = x
```

ps: 对 list 可以用[//]或:来匹配它。因为[1,2,3]本质就是 1:2:3:[]的语法糖。

as 模式就是将一个名字和@置于模式前，可以在按模式分割什么东西时仍保留对其整体的引用。

```
capital :: String -> String
capital "" = "Empty string, whoops!"
capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x
```

## 门卫
一个门卫就是一个布尔表达式，如果为真，就使用其对应的函数体。如果为假，就送去见下一个门卫，如之继续。
门卫由跟在函数名及参数后面的竖线标志，通常他们都是靠右一个缩进排成一列。
最后的那个门卫往往都是otherwise，它的定义就是简单一个otherwise = True，捕获一切。
```
bmiTell :: (RealFloat a) => a -> String   
bmiTell bmi   
    | bmi < 18.5 = "You're underweight, you emo, you!"   
    | bmi < 25.0 = "You're supposedly normal."   
    | bmi < 30.0 = "You're fat! Lose some weight, fatty!"   
    | otherwise   = "You're a whale, congratulations!" 
```

## Where
where关键字跟在门卫后面（最好是与竖线缩进一致），可以定义多个名字和函数。这些名字对每个门卫都是可见的，函数在_where_绑定中定义的名字只对本函数可见，因此我们不必担心它会污染其他函数的命名空间
```
bmiTell :: (RealFloat a) => a -> a -> String   
bmiTell weight height   
    | bmi < skinny = "You're underweight, you emo, you!"   
    | bmi < normal = "You're supposedly normal."   
    | bmi < fat = "You're fat! Lose some weight, fatty!"   
    | otherwise = "You're a whale, congratulations!"   
    where bmi = weight / height ^ 2   
          (skinny, normal, fat) = (18.5, 25.0, 30.0)  
```

## Let
let绑定与where绑定很相似。where绑定是在函数底部定义名字，对包括所有门卫在内的整个函数可见。
let绑定则是个表达式，允许你在任何位置定义局部变量，而对不同的门卫不可见。
let的格式为let [bindings] in [expressions]。在_let_中绑定的名字仅对in部分可见。
看起来无非就是，_let_把绑定放在语句前面而_where_放在后面嘛。不同之处在于，_let_绑定本身是个表达式，而_where_绑定则是个语法结构。
```
cylinder :: (RealFloat a) => a -> a -> a   
cylinder r h =  
    let sideArea = 2 * pi * r * h   
        topArea = pi * r ^2   
    in  sideArea + 2 * topArea
```

## case
模式匹配本质上不过就是case语句的语法糖而已
```
head' :: [a] -> a   
head' xs = case xs of [] -> error "No head for empty lists!"   
                      (x:_) -> x 
```
