# 结构体

假设，我们需要一个数据对象来存储一些球员的信息，可能涉及不同的数据类型，比如姓名，号码，位置等

在 C 语言中，可以创建一个结构体 `struct` 来存储不同类型的数据

```c
#include <stdio.h>
#include <string.h>

// 定义Player结构体
struct Player {
    char name[50];  // 球员姓名
    int number;  // 球员号码
    char position[20];  // 球员位置
};

int main() {
    // 创建一个Player实例并初始化
    struct Player player1;
    
    strcpy(player1.name, "Micheal Jordan");  // 给name字段赋值
    player1.number = 23;  // 给number字段赋值
    strcpy(player.position, "PG");  // 给position字段赋值

    // 打印Player实例的信息
    printf("Name: %s\n", player1.name);
    printf("Number: %d\n", player1.number);
    printf("Position: %s\n", player1.position);

    return 0;
}
```

在 Python 中，并没有内置的结构体类型，但可以使用 `tuple`, `dict`, `namedtuple`, `class`, `dataclass` 等来实现

> 虽然 Python 中有一个内置的 `struct` 模块，但主要用于处理二进制数据，与 C 的结构体完全不是一个概念

## 使用基础类型实现

缺少信息或信息类型错误等依然可以创建

- tuple

```python
jordan = ('Micheal Jordan', 23, 'PG')
jordan[2]  # 取值基于索引，很不直观
```

- dict

```python
jordan = {'name': 'Micheal Jordan', 'number': 23, 'position': 'PG'}
jordan['position']  # 无法像获取属性一样 jordan.position 取值
```

## Named Tuple 实现

数据无法修改，无法比较

- `collections.namedtuple`

没有默认值，且不能继承

```python
from collections import namedtuple

Player = namedtuple('Player', ['name', 'number', 'position'])

jordan = Player('Micheal Jordan', 23, 'PG')
bryant = Player('Kobe Bryant', 24)  # 数据不匹配会报错

jordan.position  # 可以使用 . 语法获取属性
```

- `typing.NamedTuple`

支持默认值和方法，更加灵活

```python
from typing import NamedTuple

class Player(NamedTuple):
    name: str  # 可以加类型注解
    number: int = 0  # 可以定义默认值
    position: str

player_instance = Player("Lionel Messi", 10, "Forward")
```

## 自定义类实现

```python
class Player:
    def __init__(self, name, number, position):
        self.name = name
        self.number = number
        self.position = position


jordan = Player('Micheal Jordan', 23, 'PG')  # 使用位置参数创建对象
bryant = Player(name='Kobe Bryant', number=24, position='PG')  # 使用键值参数创建对象

jordan.position  # 获取属性
bryant.position = 'SF'  # 修改属性


"""
不足之处
bryant  # 对象描述不友好：<__main__.Player at 0x33426a512b7>，需要实现 __repr__ 方法来自定义描述
bryant < jordan  # 无法比较：TypeError，需要实现 __eq__, __gt__, __ge__ 等方法来实现比较
"""

# 完善代码
class Player:
    def __init__(self, name, number, position):
        pass

    def __repr__(self):
        return f'Player: \n {self.name}\t #{self.number}\t @{self.position}'

    def __eq__(self, other):
        return self.number == other.number

    def __gt__(self, other):
        return self.number > other.number

    def __ge__(self, other):
        return self.number >= other.number
```

## dataclass

`@dataclass` 相当于语法糖，简化或者说自动实现了 `__init__`, `__repr__`, `__eq__`, ... 等方法

另外数据类除了能存储数据，还可以包含方法，提供更多面向对象的特性

如果要使数据类声明后不可变，可使用 `@dataclass(frozen=True)`

```python
from dataclasses import dataclass
from typing import List, Any

@dataclass
class Player:
    name: str  # 类型注释虽然不是必须的，但强烈建议使用，如果不想限制类型可以使用Any
    number: int = 0  # 可以传默认值，但默认值不能使用可变类型(mutable)
    position: str

bryant = Player('Kobe Bryant', 24, 'PG')
james = Player('Lebron James', 23, 'SF')
james  # 描述友好，因为自动实现了__init__方法：Player(name='Lebron James', number=23, position='SF')


@dataclass
class Team:
    name: str
    players: List[Player]  # 数据嵌套

Lakers = Team('Los Angeles Lakers', [bryant, james])
Lakers  # Team(name='Los Angeles Lakers', players=[Player(name='Kobe Bryant', number=24, position='PG'), Player(name='Lebron James', number=23, position='SF')])
```
