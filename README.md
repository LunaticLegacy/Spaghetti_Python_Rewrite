# 介绍
本项目用于对一些Python功底不太好的，或者说略微入门Python和PyTorch的深度学习人的小指南。<br>
Python是一门动态类型语言没错，但动态类型并不意味着让同一个变量在N种不同的数据类型当中随便变来变去是好习惯。<br>
一个合理的类型注解方法可以很好地解决这些问题，既可以辅助提升代码修改效率，又可增强代码可读性。<br>
（在本项目中可能会随机出现本人重构的一些Python项目代码——因为有些代码我看起来真的太那个了）<br>

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
如果实在不知道一个变量的类型，可以直接将鼠标指针挪到那个东西上面。（PyCharm上亲测有效）

## 1.3 使用typing库进行类型注解辅助
typing库是**Python内置的、用于类型注解和类型检查**的库。<br>
这里暂时介绍该库的存在，使用该库进行进阶的类型注解的内容将在**Part 2**中展开。<br>
```
from typing import List, Tuple

list1: List[int] = [1, 2, 3]              # 定义一个int类型的列表
tuple1: Tuple[int, ...] = tuple(list1)    # 定义一个内有若干个int元素的元组，其来自将list1转化为元组。
```

## 1.4 同一变量的多种可能类型
如下。
```
# 变量的情况
var9: int | str = 3    # 该变量的类型既可以是int，也可以是str。需要使用时具体分析。

# 函数的情况
def func6(var9: str | int):
    return var9 * 2
```
个人不推荐使用该方法，而是使用typing库的Union方法。具体内容请参考Part 2。

# Part 2: typing库常用内容
## 2.1 常规类型注解
略，已在1.3中提到过。

## 2.2 TypedDict
TypedDict是用于规定一个输出的字典必须具有何种字段的注解方式。目前几乎所有主流的IDE（例如PyCharm）都会引入其代码的TAB补全支持。<br>
使用方法：创建一个**继承TypedDict的类**，并使用这个类作为注解。<br>
```
from typing import TypedDict

class ResultDict(TypedDict):
    var1: int
    var2: int

dict3: ResultDict = {"var1": 3, "var2": 4}     # 使用该方法创建字典时，IDE会对字典键具有自动补全提示。
dict4: Dict[str, int] = {"var1": 3, "var2": 4} # 而这个没有

def func3(proceed_result: ResultDict) -> int:
    # 你的函数实现逻辑，总之必须返回int类型数据。
```

## 2.3 Optional
Optional的意思是“这里可能有值，也可能没有”。一般常用在**函数签名注解**。如果某个参数并非必要参数，可使用Optional[\<参数类型\>]规定。<br>
使用方法：如下。
```
from typing import Optional

def func5(var1: int, var2: Optional[int] = None) -> int:
    if var 2 is not None:
        return var1 + var2
    return var1

var99: int = func5(3, 4)         # 7
var114514: int = func5(3)        # 3
```

## 2.4 Union
Union的意思是“这里可以传入多种类型”。<br>
使用方法：如下。
```
from typing import Union

def func6(var9: Union[str, int]) -> Union[str, int]:
    return var9 * 2
```
注意：如果你使用了Union作为类型注解，请**确保代码内部逻辑可以正确处理不同的类型**。<br>
反例：
```
from typing import Union
def func12(var1: Union[int, str]) -> int:
    return var1 ** 2

# 现在如果本例中，func12的var1参数传入了一个str，那么这个东西就会立即报错：
# TypeError: unsupported operand type(s) for ** or pow(): 'str' and 'int'
```

# Part 3: 关于PyTorch库的一些小技巧
注意：本Part的内容仅为个人开发时的经验。
## 3.1 训练流（仅建议）
所有训练流中的所有模块建议全部使用**封装在torch.nn.Module的子类内**。<br>
例：
```
import torch
import torch.nn as nn

class Logistic(nn.Module):
    """
    演示用的logisitc结构。
    """
    def __init__(self, in_channels: int, out_channels: int):
        super().__init__()
        self.linear = nn.Linear(in_channels=in_channels, out_channels=out_channels)
        self.sigmoid = nn.Sigmoid()

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.sigmoid(self.linear(x))

class Model(nn.Module):
    """
    模型类。
    """
    def __init__(self):
        super().__init__()
        self.logistic = Logistic
    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.logistic(x)
```
个人不推荐将训练流的内容封装在函数内。<br>

## 3.2 反向传播过程封装
**2025/5/27 更新：亲测，最好别将反向传播过程封装在原始模型里，当模型内容包含多个模块且模块可能调度的条件不同时，将这一切封装到同一个nn.Module的派生类会导致工作量急剧增加。别问我怎么知道的。**

按照我的代码习惯，我喜欢在模型类内定义一个backward方法作为反向传播流（并封装），并在__init__方法中封装对应的损失函数、优化器及超参数调节器。<br>
并且，我喜欢在backward方法中返回当前反向传播过程损失。
例：
```
import torch
import torch.nn as nn

class Model(nn.Module):
    """
    模型类。
    """
    def __init__(self, **kwargs):    # 参数中必须包含learning_rate
        super().__init__()
        # ... 其他模型实现

        self.learning_rate = learning_rate
        self.loss_func = nn.CrossEntropyLoss()
        self.optimizer = torch.optim.AdamW(
            self.parameters(),
            lr=self.learning_rate,
            betas=(0.9, 0.999),
            weight_decay=1e-3
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        # 对应的前向传播方法实现

    def backward(self, input: torch.Tensor, target: torch.Tensor) -> float:
        loss = self.loss_func(input, target)
        loss.backward()
        self.optimizer.step()
        self.optimizer.zero_grad()
        return loss.item()

```

```
raise NotImplementedError("未完待续……作者：月と猫 - LunaNeko，最后更新日期：2025/5/27")
```
