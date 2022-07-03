---
title: Haskell学习笔记
categories: Haskell
---

本来不打算做笔记的，只是趣学指南看到第五章的时候，突然觉得不对劲

感觉有些什么东西突然变得抽象了起来

基础的内容因为已经在一份.hs文件上记录了，把简单的东西啰嗦个几遍有浮夸之嫌

到时候那份文件可能作为一个不负责任的learn x in y minutes.hs (x=haskell) 挂出来

因此这个只作为个人笔记，记录需要稍稍思考的内容和搜集到的资料云云

也可能记录一些自己的哈皮理解，笑（

还是希望自己的学习能够坚持下去

<!-- more -->

# 学习资料

《Haskell趣学指南》 (Learn you a Haskell for Great Good中文译本) $\Leftarrow$ 正在看

《Haskell函数式程序设计》(Thinking Functionally with Haskell中译本)

《Haskell函数式编程入门》 张淞大大写的

# Haskell趣学指南部分

[电子版的](https://learnyouahaskell.mno2.org/)，但是翻译较实体书好像有一点点出入，我感觉手头上的纸质翻译体感较好

代码高亮什么就不搞了，直接默认的好了，这方面的资料比较少，我暂时也不想花时间在上面

## $\S 5$ 高阶函数

1. 柯里函数

Currying，柯里化是很多语言中都有的过程，即多参数函数其实是柯里函数


2. 关于 zipWith

```haskell
ghci> zipWith' (zipWith' (*)) [[1,2,3],[3,5,6],[2,3,4]] [[3,2,2],[3,4,5],[5,4,3]]
```
乍一看很难理解，但是仔细看下函数类型

```haskell
ghci> :t zipWith' (*)
zipWith' (*) :: Num c => [c] -> [c] -> [c]
```

```haskell
ghci> :t zipWith' (zipWith' (*))
zipWith' (zipWith' (*)) :: Num c => [[c]] -> [[c]] -> [[c]]
```

然后就一目了然了，将列表的列表进行组合做乘法，返回一个列表的列表的函数罢了

3. 关于 flip



```haskell
flip' :: (a -> b -> c) -> (b -> a -> c)
flip' f = g
  where g x y = f y x
```

然后 `ghci> flip' replicate 3 5` 就会顺理成章得到 `[3,3,3,3,3]`

where 里定义了 g，对 g 有 g :: b -> (a -> c)，对 f 有 a -> (b -> c)

(a -> b -> c) -> (b -> a -> c) 与 (a -> b -> c) -> (b -> (a -> c)) 等价，这是一步Currying.

也与 (a -> b -> c) -> b -> a -> c 等价

这一步也很好理解 给定一个函数和他的参数，返回c，在计算的时候交换了顺序

然后就是理解 `flip' f y x = f x y` 这一步 注意到 f \ x \ y 都是参数 就不难理解了 一开始我死活没懂..

逻辑上就是 f = g $\Rightarrow$ f y x = g y x = f x y，合情合理