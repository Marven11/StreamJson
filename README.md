# Stream JSON Parser

一个流式JSON解析器，支持分块输入JSON数据并实时输出解析结果。

## 功能特点

- 🚀 **流式解析**: 支持分块输入JSON数据，无需等待完整JSON即可开始解析
- 🔍 **实时输出**: 在解析过程中实时输出键值对和字符串片段
- 🧩 **状态管理**: 使用状态机精确处理JSON语法，支持嵌套对象和数组
- 🌐 **Unicode支持**: 完整处理JSON转义序列，包括Unicode字符
- ⚡ **轻量高效**: 纯Python实现，无外部依赖

## 安装

### 从源码安装

```bash
# 克隆项目
# TODO: 需要提供实际仓库URL
git clone <repository-url>
cd streamjson

# 使用uv安装（推荐）
uv sync

# 或者使用pip安装
pip install .
```

### 从PyPI安装（待发布）

```bash
# TODO: 发布到PyPI后可用
pip install streamjson
```

## 快速开始

### 基础用法

```python
from streamjson.main import StreamJsonParser

# 创建解析器实例
parser = StreamJsonParser()

# 分块输入JSON数据
json_chunks = [
    '{"name": "李田所", "age": 24',
    ', "preference": ["王道征途", "泡泡浴"]}'
]

for chunk in json_chunks:
    parser.feed_string(chunk)
    for result in parser:
        print(f"{result.index_key} = {result}")
```

### 示例输出

```
name = ValuePiece(index_key='name', char='李')
name = ValuePiece(index_key='name', char='田')
name = ValuePiece(index_key='name', char='所')
name = Value(index_key='name', value='李田所')
age = Value(index_key='age', value=24)
preference.0 = ValuePiece(index_key='preference.0', char='王')
preference.0 = ValuePiece(index_key='preference.0', char='道')
preference.0 = ValuePiece(index_key='preference.0', char='征')
preference.0 = ValuePiece(index_key='preference.0', char='途')
preference.0 = Value(index_key='preference.0', value='王道征途')
```

## API文档

### 主要类

#### `StreamJsonParser`

流式JSON解析器主类。

**方法:**
- `feed_char(c: str)`: 输入单个字符进行解析
- `feed_string(s: str)`: 输入字符串进行解析
- `__iter__()`: 使解析器可迭代，返回解析结果
- `__next__() -> Value | ValuePiece`: 获取下一个解析结果

#### `Value`

完整的解析值数据类。

**属性:**
- `index_key: str`: 键的索引路径（如 "name", "preference.0"）
- `value: str | int | float | bool | None`: 解析出的值

#### `ValuePiece`

字符串值的单个字符片段数据类。

**属性:**
- `index_key: str`: 键的索引路径
- `char: str`: 单个字符

### 状态枚举

- `ParserState.OUTSIDE`: 在JSON结构外部
- `ParserState.KEY`: 正在解析键
- `ParserState.ATOMIC_VALUE`: 正在解析原子值（数字、布尔值等）
- `ParserState.STRING_VALUE`: 正在解析字符串值
- `ParserState.INVALID`: 解析器处于无效状态

## 示例

项目包含两个完整示例：

### 示例1: 基础解析演示

演示如何分块解析静态JSON字符串。

### 示例2: 文件解析演示

演示如何从文件流式读取并解析JSON数据。

运行示例：

```bash
python main.py
```

## 错误处理

解析器在遇到语法错误时会抛出`RuntimeError`异常，所有异常消息均为英文，便于国际化使用。

常见错误：
- `Stack is empty`: 栈操作时栈为空
- `Bracket mismatch`: 括号不匹配
- `Unrecognized character`: 无法识别的字符
- `Invalid parser state`: 无效的解析器状态

## 开发

### 项目结构

```
.
├── README.md          # 项目文档
├── main.py            # 示例运行入口
├── streamjson/        # 解析器主模块
│   ├── __init__.py    # 模块初始化
│   └── main.py        # 解析器实现
├── test.json          # 测试数据
└── pyproject.toml     # 项目配置
```

### 运行测试

```bash
# 其实不使用uv run也无所谓，这个项目没有第三方库依赖
uv run python main.py
```

## 贡献

给这个仓库提issue
