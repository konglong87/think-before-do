# Think Before Code Skill

一个强化 AI 编码思维的 Skill，让 AI 在编码前先思考，做到"三思而后行"。

[![GitHub stars](https://img.shields.io/github/stars/konglong87/think-before-do?style=social)](https://github.com/konglong87/think-before-do/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/konglong87/think-before-do?style=social)](https://github.com/konglong87/think-before-do/network/members)
[![GitHub watchers](https://img.shields.io/github/watchers/konglong87/think-before-do?style=social)](https://github.com/konglong87/think-before-do/watchers)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🎯 核心理念

这个 skill 基于四个核心原则，帮助 AI 更明智地编写代码：

1. **编码前先思考** - 不确定就问，不要猜
2. **简洁优先** - 能简单就不要复杂
3. **外科手术式修改** - 只碰该碰的地方
4. **目标驱动执行** - 定义成功标准，而非步骤

## 📦 安装方法

### 方式1：作为 WorkBuddy Skill 安装（推荐）

安装后 skill 会在所有项目中全局生效：

#### 选项A：从 GitHub 克隆（推荐）

```bash
# 克隆仓库到 WorkBuddy skills 目录
git clone https://github.com/konglong87/think-before-do.git ~/.workbuddy/skills/think-before-code

# 或者使用符号链接（如果你已经克隆到了其他位置）
git clone https://github.com/konglong87/think-before-do.git /path/to/think-before-do
ln -s /path/to/think-before-do ~/.workbuddy/skills/think-before-code
```

#### 选项B：下载 ZIP 文件

1. 访问 [Releases 页面](https://github.com/konglong87/think-before-do/releases)
2. 下载最新的 `think-before-code.zip`
3. 解压到 `~/.workbuddy/skills/think-before-code/`

```bash
# 快速安装命令
curl -L https://github.com/konglong87/think-before-do/archive/main.zip -o /tmp/think-before-code.zip
unzip /tmp/think-before-code.zip -d /tmp/
mv /tmp/think-before-do-main ~/.workbuddy/skills/think-before-code
```

---

### 方式2：按项目引入（项目级安装）

仅对当前项目生效，适合团队共享：

#### 新项目：直接下载 SKILL.md

```bash
# 下载 SKILL.md 到项目根目录的 .workbuddy/skills/think-before-code/
mkdir -p .workbuddy/skills/think-before-code
curl -o .workbuddy/skills/think-before-code/SKILL.md https://raw.githubusercontent.com/konglong87/think-before-do/main/SKILL.md
```

#### 已有项目追加规则

如果你的项目已经有其他 skills，可以手动合并到项目的 `SKILL.md` 中。

---

### 方式3：用于 Claude Code（CLAUDE.md）

如果你使用 Claude Code（Anthropic 的官方 CLI），可以使用 `CLAUDE.md` 版本：

```bash
# 下载到项目根目录（项目级）
curl -o CLAUDE.md https://raw.githubusercontent.com/konglong87/think-before-do/main/CLAUDE.md

# 或者下载到 home 目录（全局生效）
curl -o ~/.claude/CLAUDE.md https://raw.githubusercontent.com/konglong87/think-before-do/main/CLAUDE.md
```

**注：** `CLAUDE.md` 是 `SKILL.md` 的精简版，专门针对 Claude Code 优化。

---

### 方式4：用于 Cursor 编辑器

如果你使用 Cursor 编辑器，可以使用 `.cursorrules` 版本：

```bash
# 下载 .cursorrules 到项目根目录
curl -o .cursorrules https://raw.githubusercontent.com/konglong87/think-before-do/main/.cursorrules
```

---

## 🚀 使用方法

### 自动触发场景

安装后，这个 skill 会在以下场景自动触发：

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

---

## 📝 核心原则详解

### 原则一：编码前先思考

**规则：**
- 不确定就提问，不要猜
- 有歧义就摆出来，不要默默选一个
- 有更简单的方案就说出来，别闷头照做
- 困惑的时候停下，指出哪里不清楚

**示例：**

```
用户：帮我新增导出用户数据的功能

AI（应用原则后）：
实现前我需要先澄清几个问题：
1. 范围：导出所有用户还是筛选后的子集？
2. 格式：浏览器下载？后台任务？还是API接口？
3. 字段：要导出哪些用户字段？
4. 量级：用户量大概有多大？

最简单的方案：新增一个返回分页JSON的API接口。
```

---

### 原则二：简洁优先

**规则：**
- 不加没被要求的功能
- 不给一次性代码做抽象
- 不处理不可能发生的错误
- 200 行能压到 50 行就重写

**示例：**

```python
# ❌ 过度设计（30行）
from abc import ABC, abstractmethod

class DiscountStrategy(ABC):
    @abstractmethod
    def calculate(self, amount): pass

class PercentageDiscount(DiscountStrategy):
    def calculate(self, amount):
        return amount * (self.percentage / 100)

# ✅ 简单实现（3行）
def calculate_discount(amount, percent):
    """计算折扣金额，percent取值为0-100"""
    return amount * (percent / 100)
```

---

### 原则三：外科手术式修改

**规则：**
- 只碰该碰的地方
- 每一行改动都能追溯到用户的原始请求
- 不改进相邻代码、注释或格式
- 注意到死代码提一下，但不删

**示例：**

```diff
# 用户要求：修复空邮箱导致校验器崩溃的bug

-     if not user_data.get('email'):
+     email = user_data.get('email', '')
+     if not email or not email.strip():
          raise ValueError("邮箱为必填项")

-     if '@' not in user_data['email']:
+     if '@' not in email:
          raise ValueError("邮箱格式无效")

  # 校验用户名（保持不变）
  if not user_data.get('username'):
      raise ValueError("用户名为必填项")
```

---

### 原则四：目标驱动执行

**规则：**
- 定义成功标准，而不是步骤
- 说"做到什么程度算完"，而不是"怎么做"

**示例：**

| ❌ 不要说 | ✅ 要说 |
|-----------|---------|
| 添加输入验证 | 为无效输入写测试让它们通过 |
| 修复登录 bug | 写一个能复现登录 bug 的测试然后让它通过 |
| 重构用户服务 | 确保重构前后所有测试都通过 |

---

## 📚 更多示例

查看 [EXAMPLES.md](EXAMPLES.md) 获取详细的使用示例，包括：

- 编码前先思考的示例（隐藏的假设、多种解读可能）
- 简洁优先的示例（过度抽象、额外功能）
- 外科手术式修改的示例（顺带重构、代码风格漂移）
- 目标驱动执行的示例（模糊目标、多步骤执行、测试优先）

---

## 🎓 设计灵感

这个 skill 的设计受到 Andrej Karpathy 对 LLM 能力的观察启发：

> "LLM 最擅长的就是循环直到目标达成。你给它成功标准，它会自己折腾到对为止；你给它步骤，它反而经常走到半路跑偏。"

本项目的参考实现：[forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)

---

## 📂 文件结构

```
think-before-do/
├── SKILL.md          # WorkBuddy Skill 核心定义文件
├── CLAUDE.md         # Claude Code 版本（精简）
├── .cursorrules      # Cursor 编辑器版本
├── EXAMPLES.md       # 详细使用示例
└── README.md         # 本说明文档
```

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

**贡献方式：**
1. Fork 这个仓库
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的改动 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 打开一个 Pull Request

---

## 📄 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件（如果有）

---

## 💡 使用示例

### 示例1：实现新功能

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

## 🌟 Star History

[![Star History Chart](https://api.star-history.com/svg?repos=konglong87/think-before-do&type=Date)](https://star-history.com/#konglong87/think-before-do&Date)

---

## 📊 效果对比

### 使用前的典型对话

**用户：** 帮我写一个函数，读取 CSV 文件并返回数据

**AI：**（直接写出30行过度设计的代码）

---

### 使用后的典型对话

**用户：** 帮我写一个函数，读取 CSV 文件并返回数据

**AI：**
```
在写代码前，我想确认一下：
1. 数据量大概有多大？
2. 返回格式你偏好列表还是字典？
3. 需要处理特殊的 CSV 格式吗？

如果就是简单的读取，我可以这样写：
（5行简单代码）

这个实现简单直接，使用 Python 标准库，无额外依赖。
如果这不符合你的需求，请告诉我，我来调整。
```

---

## 🔗 相关项目

- [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) - 本项目的参考实现
- [WorkBuddy Documentation](https://www.codebuddy.cn/docs/workbuddy/skills) - WorkBuddy Skill 官方文档

---

## 👤 作者

**konglong87**

- GitHub: [@konglong87](https://github.com/konglong87)

---

**让 AI 编码更明智，从思考开始。** 🚀
