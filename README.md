# 介绍
本项目用于对一些Python功底不太好的，或者说略微入门Python和PyTorch的深度学习人的小指南。<br>
Python是一门动态类型语言没错，但动态类型并不意味着让同一个变量在N种不同的数据类型当中随便变来变去是好习惯。<br>
一个合理的类型注解方法可以很好地解决这些问题，既可以辅助提升代码修改效率，又可增强代码可读性。<br>

# Part 1: 类型注解
类型注解是一个在Python开发时的可有可无的东西，但强烈建议将类型注解做上。<br>
尤其是当一个名称的变量类型可能在执行期间发生改变时。<br>

## 1.1 变量名类型注解
```
from PIL import Image

img = "image_path"     # 类型：str
img = Image.open(img)  # 类型：PIL.Image
```
可以改为：
```
from PIL import Image

img: str = "image_path"       # 类型：str
img: Image = Image.open(img)  # 类型：PIL.Image
```
这样做可以追踪某个类型的变量。<br>
强烈建议**初次声明变量或变量类型改变时**进行注解。上述内容即为例。

## 1.2 函数类型注解
原始函数：
```
def func1(var1):
    return var1 ** 2
```

改为：
```
def func1(var1: int) -> int:
    return var1 ** 2
```
这样做可用于规定一个函数需使用何种类型。<br>
在修改后，其他需要使用该函数的用户可以明确var1需要传入一个int类型变量，并且会返回一个int类型变量，而不是传入一个str类型，并报错：<br>
```
TypeError: unsupported operand type(s) for ** or pow(): 'str' and 'int'
```
p.s.<br>
如果实在不知道一个函数

## 1.3 使用typing库进行类型注解辅助
typing库是**Python内置的、用于类型注解和类型检查**的库。<br>
这里暂时介绍该库的存在，使用该库进行进阶的类型注解的内容将在**Part 3**中展开。<br>
```
from typing import List, Tuple

list1: List[int] = [1, 2, 3]              # 定义一个int类型的列表
tuple1: Tuple[int, ...] = tuple(list1)    # 定义一个内有若干个int元素的元组，其来自将list1转化为元组。
```

```
raise NotImplementedError("未完待续……作者：月と猫 - LunaNeko，最后更新日期：2025/3/25")
```
