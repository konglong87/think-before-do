# 示例

本页面演示四大核心原则的真实代码示例，每个示例都会展示 AI 在辅助开发时的常见错误做法，以及对应的修正方案。

---

## 1. 编码前先思考

### 示例1：隐藏的假设

**用户请求：**「新增导出用户数据的功能」

**❌ AI 的常见错误（做出错误假设）**

```python
def export_users(format='json'):
    """导出所有用户到文件"""
    users = User.query.all()

    if format == 'json':
        with open('users.json', 'w') as f:
            json.dump([u.to_dict() for u in users], f)
    elif format == 'csv':
        with open('users.csv', 'w') as f:
            writer = csv.DictWriter(f, fieldnames=['id', 'email', 'name'])
            writer.writeheader()
            writer.writerows([u.to_dict() for u in users])

    return f"已导出{len(users)}个用户"
```

**问题点：**
- 默认要导出所有用户（未考虑分页、隐私问题）
- 未询问就默认文件存储位置
- 默认要导出的字段，未确认
- 默认CSV的列名，未核对实际数据结构

**✅ 正确做法（暴露假设）**

```
实现前我需要先澄清几个问题：
1. **范围**：导出所有用户还是筛选后的子集？（涉及隐私影响）
2. **格式**：你提到「导出」，具体是指：
   - 浏览器下载文件？
   - 后台任务完成后邮件通知？
   - 返回数据的API接口？
3. **字段**：要导出哪些用户字段？（部分字段可能敏感）
4. **量级**：用户量通常有多大？（影响实现方案）

最简单的方案：新增一个返回分页JSON的API接口。如果需要文件类导出，还需要更多信息。

你的偏好是什么？
```

---

### 示例2：多种解读可能

**用户请求：**「让搜索速度更快」

**❌ AI 的常见错误（默认选一种方案）**

```python
# 未询问用户需求，就直接新增缓存、数据库索引、异步处理
# 不明确用户想要的「更快」是哪种
@lru_cache(maxsize=1000)
async def search(query: str) -> List[Result]:
    # ... 200行优化代码
```

**✅ 正确做法**

```
「让搜索速度更快」可能有多重含义：
1. **更短的响应时间**（从500ms降到100ms以内）
   - 新增数据库索引
   - 缓存高频查询
   - 预估工作量：2小时

2. **支持更高并发搜索**（提升吞吐量）
   - 新增async/await语法
   - 连接池优化
   - 预估工作量：4小时

3. **更快的感知速度**（优化用户体验）
   - 立即展示部分结果
   - 渐进式加载
   - 预估工作量：3小时

当前搜索在典型查询下耗时约500ms。
你的用例中最看重哪个方面？
```

---

## 2. 简洁优先

### 示例1：过度抽象

**用户请求：**「新增一个计算折扣的函数」

**❌ AI 的常见错误（过度设计）**

```python
from abc import ABC, abstractmethod
from enum import Enum
from typing import Protocol, Union
from dataclasses import dataclass

class DiscountStrategy(ABC):
    @abstractmethod
    def calculate(self, amount: float) -> float:
        pass

class PercentageDiscount(DiscountStrategy):
    def __init__(self, percentage: float):
        self.percentage = percentage

    def calculate(self, amount: float) -> float:
        return amount * (self.percentage / 100)

class FixedDiscount(DiscountStrategy):
    def __init__(self, fixed_amount: float):
        self.fixed_amount = fixed_amount

    def calculate(self, amount: float) -> float:
        return min(self.fixed_amount, amount)

@dataclass
class DiscountConfig:
    strategy: DiscountStrategy
    min_purchase: float = 0.0
    max_discount: float = float('inf')

class DiscountCalculator:
    def __init__(self, config: DiscountConfig):
        self.config = config

    def apply_discount(self, amount: float) -> float:
        if amount < self.config.min_purchase:
            return 0
        discount = self.config.strategy.calculate(amount)
        return min(discount, self.config.max_discount)

# 一个简单的计算需要30+行初始化代码才能使用
```

**✅ 正确做法（简单实现）**

```python
def calculate_discount(amount: float, percent: float) -> float:
    """计算折扣金额，percent取值为0-100"""
    return amount * (percent / 100)

# 使用方式
discount = calculate_discount(100.0, 10.0)  # 减免10
```

**何时增加复杂度：** 只有当你确实需要多种折扣类型时再升级。如果后续有这个需求，再重构即可。

---

### 示例2：额外添加未要求的功能

**用户请求：**「把用户偏好保存到数据库」

**❌ AI 的常见错误（新增不必要的功能）**

```python
class PreferenceManager:
    def __init__(self, db, cache=None, validator=None):
        self.db = db
        self.cache = cache or InMemoryCache()
        self.validator = validator or DefaultValidator()

    def save(self, user_id: int, prefs: dict,
             merge: bool = True,
             validate: bool = True,
             notify: bool = False) -> bool:
        """保存偏好，支持可选的合并、校验、通知功能"""

        if validate:
            errors = self.validator.validate(prefs)
            if errors:
                raise ValidationError(errors)

        existing = self.get(user_id) if merge else {}
        if merge:
            existing.update(prefs)
            prefs = existing

        self.db.execute(
            "UPDATE users SET preferences = ? WHERE id = ?",
            (json.dumps(prefs), user_id)
        )

        if self.cache:
            self.cache.set(f"prefs:{user_id}", prefs)

        if notify:
            self.notify_preference_change(user_id, prefs)

        return True

    def notify_preference_change(self, user_id: int, prefs: dict):
        # 额外30行没人要求的通知功能代码
        pass
```

**✅ 正确做法（只实现需求内容）**

```python
def save_preferences(db, user_id: int, preferences: dict):
    """将用户偏好保存到数据库"""
    db.execute(
        "UPDATE users SET preferences = ? WHERE id = ?",
        (json.dumps(preferences), user_id)
    )
```

**后续按需新增：** 缓存（性能出现问题时再加）、校验（出现脏数据时再加）、合并（有对应需求时再加）。

---

## 3. 外科手术式修改

### 示例1：顺带重构

**用户请求：**「修复空邮箱导致校验器崩溃的bug」

**❌ AI 的常见错误（修改内容过多）**

```diff
  def validate_user(user_data):
-     # 校验邮箱格式
-     if not user_data.get('email'):
+     """校验用户数据"""
+     email = user_data.get('email', '').strip()
+
+     # 校验邮箱
+     if not email:
          raise ValueError("邮箱为必填项")
-
-     # 基础邮箱校验
-     if '@' not in user_data['email']:
+     if '@' not in email or '.' not in email.split('@')[1]:
          raise ValueError("邮箱格式无效")
+
      # 校验用户名
-     if not user_data.get('username'):
+     username = user_data.get('username', '').strip()
+     if not username:
          raise ValueError("用户名为必填项")
+     if len(username) < 3:
+         raise ValueError("用户名过短")
+     if not username.isalnum():
+         raise ValueError("用户名必须仅包含字母和数字")
+
      return True
```

**问题点：**
- 超出bug修复范围，优化了邮箱校验逻辑
- 新增了没人要求的用户名校验规则
- 修改了注释
- 新增了文档字符串

**✅ 正确做法（精准修改）**

```diff
  def validate_user(user_data):
      # 校验邮箱格式
-     if not user_data.get('email'):
+     email = user_data.get('email', '')
+     if not email or not email.strip():
          raise ValueError("邮箱为必填项")

      # 基础邮箱校验
-     if '@' not in user_data['email']:
+     if '@' not in email:
          raise ValueError("邮箱格式无效")

      # 校验用户名
      if not user_data.get('username'):
          raise ValueError("用户名为必填项")

      return True
```

**仅修改内容：** 专门修复空邮箱处理问题的相关代码行，其他内容完全不动。

---

### 示例2：代码风格漂移

**用户请求：**「给上传函数新增日志功能」

**❌ AI 的常见错误（重构所有格式）**

```diff
- def upload_file(file_path, destination):
+ def upload_file(file_path: str, destination: str) -> bool:
+     """上传文件到目标路径并记录日志"""
+     logger.info(f"正在上传{file_path}到{destination}")
+
      try:
-         with open(file_path, 'rb') as f:
-             data = f.read()
-
-         response = requests.post(destination, files={'file': data})
-
-         if response.status_code == 200:
-             return True
-         else:
-             return False
+         with open(file_path, "rb") as f:
+             data = f.read()
+         response = requests.post(destination, files={"file": data})
+         success = response.status_code == 200
+
+         if success:
+             logger.info(f"上传成功：{file_path}")
+         else:
+             logger.error(f"上传失败：{file_path}，状态码={response.status_code}")
+
+         return success
      except Exception as e:
-         print(f"Error: {e}")
+         logger.exception(f"上传{file_path}出错：{e}")
          return False
```

**问题点：**
- 修改了引号风格（单引号改双引号）
- 新增了没人要求的类型注解
- 新增了文档字符串
- 重构了空白格式
- 修改了布尔值返回逻辑

**✅ 正确做法（匹配现有风格）**

```diff
+ import logging
+
+ logger = logging.getLogger(__name__)
+
  def upload_file(file_path, destination):
+     logger.info(f'开始上传：{file_path}')
      try:
          with open(file_path, 'rb') as f:
              data = f.read()

          response = requests.post(destination, files={'file': data})

          if response.status_code == 200:
+             logger.info(f'上传成功：{file_path}')
              return True
          else:
+             logger.error(f'上传失败：{file_path}，状态码={response.status_code}')
              return False
      except Exception as e:
-         print(f"Error: {e}")
+         logger.exception(f'上传出错：{file_path}')
          return False
```

**匹配内容：** 单引号、无类型注解、原有的布尔返回逻辑、原有空格风格，和现有代码完全保持一致。

---

## 4. 目标驱动执行

### 示例1：模糊目标 vs 可验证目标

**用户请求：**「修复认证系统」

**❌ AI 的常见错误（模糊的执行方式）**

```
我将通过以下步骤修复认证系统：
1. 审查代码
2. 识别问题
3. 做优化
4. 测试变更
[直接开始修改，没有明确的成功标准]
```

**✅ 正确做法（可验证的目标）**

```
要「修复认证系统」，我需要先定义成功标准。你要解决的具体问题是什么？

如果问题是「用户修改密码后仍然保持登录状态」：
执行计划：
1. 编写测试：修改密码 → 验证旧会话是否失效
   验证：测试失败（复现bug）

2. 实现功能：在密码修改时使所有会话失效
   验证：测试通过

3. 检查边界情况：多活跃会话、并发修改的情况
   验证：额外测试通过

4. 验证无回归问题：现有认证测试仍然全部通过
   验证：全测试套件通过

当前认证模块的测试覆盖率：[展示覆盖率]

你遇到的具体认证问题是什么？
```

---

### 示例2：多步骤执行+验证

**用户请求：**「给API新增限流功能」

**❌ AI 的常见错误（一次性全部实现）**

```python
# 一次性实现完整的限流功能，包含Redis、多种策略、配置系统、监控，
# 单次提交300行代码，没有明确的验证步骤
```

**✅ 正确做法（增量实现+验证）**

```
限流功能实现计划：

1. 新增基础内存限流（针对单个接口）
   验证：
   - 测试：发起100次请求 → 前10次成功，其余返回429状态码
   - 手动验证：用curl调用接口11次，确认触发限流错误

2. 抽成中间件（应用到所有接口）
   验证：
   - 测试：限流规则应用到/users和/posts接口
   - 现有接口测试仍然全部通过

3. 新增Redis后端（支持多服务器部署）
   验证：
   - 测试：应用重启后限流计数仍然保留
   - 测试：两个应用实例共享限流计数器

4. 新增配置功能（不同接口不同限流规则）
   验证：
   - 测试：/search接口限流10次/分钟，/users接口限流100次/分钟
   - 配置文件解析正确

每个步骤都可以独立验证、独立部署。

是否从第一步开始？
```

---

### 示例3：测试优先验证

**用户请求：**「存在重复分数时排序会出错」

**❌ AI 的常见错误（不先复现就直接修复）**

```python
# 没有确认bug就直接修改排序逻辑
def sort_scores(scores):
    return sorted(scores, key=lambda x: (-x['score'], x['name']))
```

**✅ 正确做法（先复现问题）**

```python
# 1. 首先编写能复现问题的测试
def test_sort_with_duplicate_scores():
    """测试多个条目分数相同时的排序逻辑"""
    scores = [
        {'name': 'Alice', 'score': 100},
        {'name': 'Bob', 'score': 100},
        {'name': 'Charlie', 'score': 90},
    ]

    result = sort_scores(scores)

    # 问题：重复分数的条目顺序是不确定的
    # 多次运行这个测试，顺序应该保持一致
    assert result[0]['score'] == 100
    assert result[1]['score'] == 100
    assert result[2]['score'] == 90

# 验证：运行测试10次 → 因顺序不一致导致测试失败

# 2. 现在修复，使用稳定排序
def sort_scores(scores):
    """按分数降序排序，分数相同时按名字升序排序"""
    return sorted(scores, key=lambda x: (-x['score'], x['name']))

# 验证：测试始终通过
```

---

## 反模式汇总

| 原则 | 反模式 | 修正方案 |
|------|--------|----------|
| 编码前先思考 | 默认文件格式、字段、范围 | 明确列出所有假设，向用户确认 |
| 简洁优先 | 单个折扣计算就用策略模式 | 先写一个函数，等确实需要复杂度时再升级 |
| 外科手术式修改 | 修复bug时同时重构引号、加类型注解 | 只修改和报告问题相关的代码行 |
| 目标驱动 | 「我会审查并优化代码」 | 「编写X bug的复现测试 → 让测试通过 → 验证无回归问题」 |

---

## 核心洞察

这些「过度复杂」的示例并不是明显错误的——它们都符合设计模式和最佳实践。问题在于**时机**：它们在需要复杂度之前就提前引入了复杂度，这会导致：

- 代码更难理解
- 引入更多bug
- 实现耗时更长
- 更难测试

而「简单」版本的优势是：

- 更容易理解
- 实现速度更快
- 更容易测试
- 等后续确实需要复杂度时，可以再重构

**好的代码是简单解决当下问题的代码，而不是过早解决未来问题的代码。**
