# Think Before Code Skill

一个强化 AI 编码思维的 WorkBuddy Skill，让 AI 在编码前先思考，做到"三思而后行"。

## 🎯 核心理念

这个 skill 基于四个核心原则，帮助 AI 更明智地编写代码：

1. **编码前先思考** - 不确定就问，不要猜
2. **简洁优先** - 能简单就不要复杂
3. **外科手术式修改** - 只碰该碰的地方
4. **目标驱动执行** - 定义成功标准，而非步骤

## 📦 安装方法

### 方法一：从 GitHub 安装（推荐）

```bash
# 克隆仓库
git clone https://github.com/konglong87/think-before-do.git

# 复制到 WorkBuddy skills 目录
cp -r think-before-do ~/.workbuddy/skills/think-before-code
```

### 方法二：从发布页面下载

1. 访问 [Releases 页面](https://github.com/konglong87/think-before-do/releases)
2. 下载最新的 `think-before-code.zip`
3. 解压到 `~/.workbuddy/skills/think-before-code/`

## 🚀 使用方法

安装后，这个 skill 会在以下场景自动触发：

### 自动触发场景

- 开始任何编码任务时
- 修复 bug 时
- 重构代码时
- 当 AI 似乎过于匆忙地开始编码时

### 手动触发

在对话中明确提到：

```
使用 think-before-code skill
```

或者：

```
@skill://think-before-code 帮我实现这个功能
```

## 📝 核心原则详解

### 原则一：编码前先思考

**规则：**
- 不确定就提问，不要猜
- 有歧义就摆出来，不要默默选一个
- 有更简单的方案就说出来，别闷头照做
- 困惑的时候停下，指出哪里不清楚

**错误示例：**
```python
# AI 看到需求后立刻开始写代码，但没有理解真正的需求
def process_data(data):
    # 假设数据一定是列表格式
    return [x * 2 for x in data]  # 如果 data 是字典怎么办？
```

**正确做法：**
> "我看到你要处理数据，能告诉我数据的确切格式吗？是列表、字典还是其他格式？这样我可以写出更准确的代码。"

---

### 原则二：简洁优先

**规则：**
- 不加没被要求的功能
- 不给一次性代码做抽象
- 不处理不可能发生的错误
- 200 行能压到 50 行就重写

**错误示例：**
```python
# 过度抽象的一次性代码
from abc import ABC, abstractmethod

class DataProcessor(ABC):
    @abstractmethod
    def process(self, data):
        pass

class NumberDoubler(DataProcessor):
    def process(self, data):
        return [x * 2 for x in data]

# 如果只用一次，为什么要这么复杂？
```

**正确做法：**
```python
# 简单直接
def double_numbers(numbers):
    return [x * 2 for x in numbers]
```

**简洁性测试：**
> 一个资深工程师看完你的代码，会不会觉得太复杂？如果会，就简化。

---

### 原则三：外科手术式修改

**规则：**
- 只碰该碰的地方
- 每一行改动都能追溯到用户的原始请求
- 不改进相邻代码、注释或格式
- 注意到死代码提一下，但不删

**错误示例：**
```python
# 用户要求：修复 login() 函数中的 bug
def login(username, password):
    # 修复了 bug ✓
    if not password:
        return False

# 顺手"改进"了相邻的代码 ✗
def logout(session):
    # 改进了这里的错误处理（用户没要求）
    try:
        session.clear()
    except Exception as e:
        logger.error(f"Logout error: {e}")  # 原来没有日志
```

**正确做法：**
```python
# 用户要求：修复 login() 函数中的 bug
def login(username, password):
    # 修复了 bug ✓
    if not password:
        return False

# logout() 函数保持不变，即使看到可以改进的地方
def logout(session):
    session.clear()
```

**检验标准：**
> 每一行改动都能直接追溯到用户的原始请求。

---

### 原则四：目标驱动执行

**规则：**
- 定义成功标准，而不是步骤
- 说"做到什么程度算完"，而不是"怎么做"

**为什么这样有效？**

LLM 最擅长的是循环直到目标达成。当你给它成功标准，它会自己折腾到对为止；当你给它步骤，它反而经常走到半路跑偏。

**对比示例：**

| ❌ 不要说 | ✅ 要说 |
|-----------|---------|
| 添加输入验证 | 为无效输入写测试让它们通过 |
| 修复登录 bug | 写一个能复现登录 bug 的测试然后让它通过 |
| 重构用户服务 | 确保重构前后所有测试都通过 |

**实际应用：**

```
# 错误方式（给出步骤）
请按以下步骤操作：
1. 添加输入验证
2. 添加错误处理
3. 添加日志
4. 写测试

# 正确方式（定义成功标准）
请实现一个用户注册功能，成功标准是：
- 所有无效输入（空用户名、短密码等）都有测试用例并能通过
- 代码简洁，不超过 50 行
- 匹配项目现有的代码风格
```

## 🎓 设计灵感

这个 skill 的设计受到 Andrej Karpathy 对 LLM 能力的观察启发：

> "LLM 最擅长的就是循环直到目标达成。你给它成功标准，它会自己折腾到对为止；你给它步骤，它反而经常走到半路跑偏。"

## 📂 文件结构

```
think-before-code/
├── SKILL.md          # Skill 核心定义文件
└── README.md         # 本说明文档
```

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

**贡献方式：**
1. Fork 这个仓库
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的改动 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 打开一个 Pull Request

## 📄 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件（如果有）

## 💡 使用示例

### 示例 1：实现新功能

**用户提问：**
```
帮我实现一个函数，计算斐波那契数列的第 n 项
```

**AI 的思考过程（应用 Skill）：**

1. **思考** - 用户要的是简单的计算，还是要处理大数？要不要缓存？
2. **简洁** - 先写最简单的递归或迭代，不要一开始就上记忆化
3. **目标** - 成功标准是"给定 n，返回正确的斐波那契数"

**AI 回复：**
```
在写代码前，我想确认几个问题：
1. n 的范围大概是多少？如果很大，我需要考虑性能优化
2. 要不要处理负数或非法输入？
3. 是要简单的实现，还是需要考虑性能？

如果我按照最简单的方式实现，大概这样：
```python
def fib(n):
    if n <= 1:
        return n
    return fib(n-1) + fib(n-2)
```

但这对大 n 会很慢。你更看重简单还是性能？
```

---

### 示例 2：修复 Bug

**用户提问：**
```
我的代码有个 bug，登录时输入正确密码也登录失败
```

**AI 的思考过程（应用 Skill）：**

1. **思考** - 不要猜原因，先问清楚情况
2. **目标** - 成功标准是"写出能复现 bug 的测试，然后让测试通过"
3. **简洁** - 不要顺手"改进"其他代码

**AI 回复：**
```
我来帮你修复这个 bug。为了更好地定位问题，我需要了解：

1. 能给我看一下登录相关的代码吗？
2. 密码是明文比较还是哈希比较？
3. 有没有错误日志或具体报错信息？

另外，我会先写一个能复现这个 bug 的测试，然后修复它。
这样能确保问题真正解决了，而不仅仅是从表面上看起来修复了。

请把相关代码发给我，我来分析。
```

---

### 示例 3：代码审查

**用户提问：**
```
帮我审查这段代码，看看有什么问题
```

**AI 的思考过程（应用 Skill）：**

1. **外科手术式** - 只指出问题，不顺便重构
2. **简洁** - 只说关键问题，不说废话
3. **思考** - 先理解代码意图，再审查

**AI 回复：**
```
我看了一下这段代码，有几个问题：

**关键问题：**
1. 第 12 行：没有处理 `user_dict` 为 `None` 的情况，可能导致 `KeyError`
2. 第 18 行：密码比较用了 `==` 而不是安全的比较方式

**建议修改：**
```python
# 第 12 行改为：
if not user_dict:
    return None

# 第 18 行改为：
if not secrets.compare_digest(user_dict['password_hash'], hash_password(password)):
    return None
```

**注意：** 我只修改了有问题的地方，没有重构其他部分。如果你需要进一步的代码优化，请告诉我。

另外，我注意到第 25-30 行有一些未使用的变量，但没有改动它们。如果需要清理，请明确告诉我。
```

---

### 示例 4：重构代码

**用户提问：**
```
帮我重构这个函数，它太复杂了
```

**AI 的思考过程（应用 Skill）：**

1. **目标驱动** - 成功标准是"重构前后测试都通过"
2. **简洁** - 能简化就简化，但不要过度设计
3. **思考** - 先理解函数的职责，再重构

**AI 回复：**
```
在重构之前，让我先确认成功标准：

**重构目标：**
1. 确保所有现有测试在重构前后都能通过（保证行为不变）
2. 降低复杂度，让代码更容易理解
3. 保持或提升性能

**我的重构策略：**
1. 先写测试覆盖当前行为（如果还没有的话）
2. 找出函数的核心职责
3. 拆分成更小的函数，每个函数只做一件事
4. 确保测试全部通过

我开始重构了。如果你在任何时候想调整方向，请告诉我。

另外，我注意到这个文件和项目其他部分的代码风格不太一致。我应该：
- A. 保持项目现有风格（推荐）
- B. 改成更现代的 Python 风格

你更倾向哪种？
```

---

## 🔧 进阶技巧

### 1. 结合其他 Skill 使用

这个 skill 可以和其他编码相关的 skill 结合使用：

```
# 使用 pdf skill 前，先思考
@skill://think-before-code 我需要编辑一个 PDF，帮我思考一下最佳方案

# 使用 xlsx skill 前，先思考
@skill://think-before-code 我需要生成一个复杂的 Excel 报表，先帮我规划一下数据结构
```

### 2. 在复杂任务中显式调用

对于复杂任务，显式调用这个 skill 可以让你看到 AI 的思考过程：

```
@skill://think-before-code 我要实现一个分布式任务队列，帮我先思考架构设计
```

### 3. 用于代码审查

让 AI 用这个 skill 的思维来审查代码：

```
@skill://think-before-code 帮我审查这段代码，特别关注是否有过度设计的地方
```

---

## ❓ 常见问题

### Q: 这个 skill 会让 AI 变慢吗？

A: 不会。这个 skill 只是改变 AI 的**思维方式**，不会增加额外的工具调用或等待时间。

### Q: 我可以只使用部分原则吗？

A: 可以。你可以在 `SKILL.md` 中注释掉你不需要的原则。但建议完整使用，因为四个原则是相互补充的。

### Q: 这个 skill 适用于所有编程语言吗？

A: 是的。这四个原则是通用的编码思维，适用于任何编程语言。

### Q: 如果 AI 还是写了很多冗余代码怎么办？

A: 你可以在对话中提醒 AI：
```
记住 think-before-code skill 的原则：简洁优先。这段代码能不能简化？
```

---

## 📊 效果对比

### 使用前的典型对话

**用户：** 帮我写一个函数，读取 CSV 文件并返回数据

**AI：**
```python
import pandas as pd
import numpy as np
from typing import List, Dict, Optional, Union
import logging

class CSVReader:
    def __init__(self, file_path: str, delimiter: str = ',', encoding: str = 'utf-8'):
        self.file_path = file_path
        self.delimiter = delimiter
        self.encoding = encoding
        self.logger = logging.getLogger(__name__)

    def read(self) -> List[Dict]:
        try:
            df = pd.read_csv(self.file_path, delimiter=self.delimiter, encoding=self.encoding)
            return df.to_dict('records')
        except Exception as e:
            self.logger.error(f"Error reading CSV: {e}")
            raise

def read_csv_file(file_path: str, delimiter: str = ',', encoding: str = 'utf-8') -> List[Dict]:
    reader = CSVReader(file_path, delimiter, encoding)
    return reader.read()
```

**问题：** 过度设计，引入了不必要的抽象和依赖。

---

### 使用后的典型对话

**用户：** 帮我写一个函数，读取 CSV 文件并返回数据

**AI：**
```
在写代码前，我想确认一下：

1. 数据量大概有多大？如果很大，我可能需要用流式读取
2. 返回格式你偏好列表还是字典？
3. 需要处理特殊的 CSV 格式吗（如引号、换行等）？

如果就是简单的读取，我可以这样写：

```python
import csv

def read_csv(file_path):
    with open(file_path, 'r') as f:
        reader = csv.DictReader(f)
        return list(reader)
```

这个实现：
- 简单直接，不到 5 行代码
- 使用 Python 标准库，无额外依赖
- 返回字典列表，方便使用

如果这不符合你的需求，请告诉我，我来调整。
```

---

## 🌟 最佳实践

### 1. 在对话开始时明确提及

```
@skill://think-before-code 帮我实现这个功能
```

### 2. 鼓励 AI 提问

如果 AI 没有提问就开始写代码，你可以说：
```
先别写代码，你有什么不清楚的地方吗？
```

### 3. 要求简洁

如果 AI 给出了复杂的实现，你可以说：
```
这段代码能不能简化到 50 行以内？
```

### 4. 验证改动范围

如果 AI 做了很多改动，你可以问：
```
这些改动都能追溯到我的原始需求吗？
```

---

## 📚 参考资料

- [Andrej Karpathy 的 LLM 观察](https://karpathy.github.io/)
- [WorkBuddy Skill 创建指南](https://www.codebuddy.cn/docs/workbuddy/skills)
- [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)

---

## 👤 作者

**konglong87**

- GitHub: [@konglong87](https://github.com/konglong87)

---

## ⭐ Star History

如果这个 skill 对你有帮助，请给个 Star！ ⭐

---

**让 AI 编码更明智，从思考开始。** 🚀
