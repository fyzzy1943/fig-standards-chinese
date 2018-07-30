# 基本编码标准

原文：[PSR-0]

标准的这一部分包括应该被认为是确保共享PHP代码之间高水平技术互操作性所需的标准编码元素。

关键词对应关系：
- 必须（MUST）
- 一定不可（MUST NOT）
- 需要（REQUIRED）
- 将会（SHALL）
- 不会（SHALL NOT）
- 应该（SHOULD）
- 不应该（SHOULD NOT）
- 推荐（RECOMMENDED）
- 可以（MAY）
- 可选（OPTIONAL）

详细描述可参见 [RFC 2119] 。

[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[PSR-4]: https://github.com/php-fig/fig-standards/blob/master/accepted/

## 1. 概览

- 文件必须只使用 `<?php` 和 `<?=` 标签。
- PHP代码文件必须使用 `无BOM的UTF-8` 格式编码。
- 文件应该做出声明（类，函数，常量等）或引起其他效果（例如生成文件，更改.ini设置等），但不应该同时执行这两种操作。
- 命名空间和类必须遵循一个自动加载PSR，[[PSR-0]，[PSR-4]
- 类名必须使用大写字母开头的驼峰式命名，`StudlyCaps`。
- 类常量必须使用以下划线分隔的大写字母命名。
- 方法名称必须使用驼峰示命名，`camelCase`。

## 2. 文件

### 2.1. PHP标签

PHP代码必须使用长标签 `<?php ?>` 或短输出标签 `<?= ?>`，一定不可使用其他标签。

### 2.2. 字符编码

PHP代码必须只能使用 `无BOM的UTF-8` 编码。

### 2.3. 副作用

一个文件应该包含声明（类，函数，常量，等等）不会引起其他副作用，或者应该执行包含副作用的逻辑，但是不应该同时做这两件事。

短语“副作用”意味着仅仅通过包含文件来执行与声明类，函数，常数等不直接相关的逻辑。

“副作用”包括但不限于：生成输出，明确使用require或include，连接到外部服务，修改ini设置，发出错误或异常，修改全局或静态变量，读取或写入文件等等。

以下是具有声明和副作用的文件示例，即要避免的一个例子：

```php
<?php
// 副作用: 改变ini配置
ini_set('error_reporting', E_ALL);

// 副作用: 加载文件
include "file.php";

// 副作用: 生产输出
echo "<html>\n";

// 声明
function foo()
{
 // function body
}
```

以下示例是包含没有副作用的声明文件，即可以模仿的例子：

```php
<?php
// 声明
function foo()
{
 // function body
}

// 条件声明*不是*副作用
if (! function_exists('bar')) {
 function bar()
 {
 // function body
 }
}
```

## 3. 命名空间和类名

命名空间和类必须遵循“自动加载”PSR：[[PSR-0，PSR-4]]。

这意味着每个类都在一个和它同名的文件里，并且至少在一个顶级命名空间下：顶级的vendor名称。

类名必须使用大写字母开头的驼峰命名，`StudlyCaps`。

PHP 5.3 及以后版本写的代码，必须使用正式名称空间。

例如：

```php
<?php
// PHP 5.3 及以后:
namespace Vendor\Model;

class Foo
{
}
```

PHP 5.2.X 及以前版本写的代码，应该使用伪命名空间的写法，在类名前增加 `Vendor_` 前缀。

```php
<?php
// PHP 5.2.x 及以前:
class Vendor_Model_Foo
{
}
```

## 4. 类常量，属性和方法

“类”指的是所有类（class），接口（interface）和特征（trait）。

### 4.1. 常量

类常量必须以全部大写形式声明，单词以下划线分隔，例如：

```php
<?php
namespace Vendor\Model;

class Foo
{
 const VERSION = '1.0';
 const DATE_APPROVED = '2012-06-01';
}
```

### 4.2. 属性

本指南有意避免任何有关使用 `$StudlyCaps`，`$camelCase` 或 `$under_score` 属性名称的建议。

但无论使用何种命名约定，都应该在合理的范围内始终保持一致。该范围可以是vendor级别，包级别，类级别或方法级别。

### 4.3. 方法

方法名必须在
