递归实际上是定义函数以调用自身的方式
递归在haskell中至关重要。命令式语言要求你提供求解的步骤，haskell则倾向于让你提供问题的描述。
这便是haskell没有while或for循环的原因，递归是我们的替代方案。

```
maximum' :: (Ord a) => [a] -> a   
maximum' [] = error "maximum of empty list"   
maximum' [x] = x   
maximum' (x:xs)    
    | x > maxTail = x   
    | otherwise = maxTail   
    where maxTail = maximum' xs
```

无限List
```
repeat' :: a -> [a]   
repeat' x = x:repeat' x
```

快速排序
```
quicksort :: (Ord a) => [a] -> [a]  
quicksort [] = []  
quicksort (x:xs) =   
    let smallerSorted = quicksort [a | a <- xs, a <= x]  
        biggerSorted = quicksort [a | a <- xs, a > x]  
    in  smallerSorted ++ [x] ++ biggerSorted 
```

固定模式：先定义一个边界条件，再定义个函数，让它从一堆元素中取一个并做点事情后，把余下的元素重新交给这个函数