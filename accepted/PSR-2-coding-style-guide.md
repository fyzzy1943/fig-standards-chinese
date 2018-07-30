# 编码风格指南

本篇规范基于基本编码标准 [PSR-1] ，并在此之上进行扩展。

本篇规范的意图在于，减少在浏览来自不同作者的代码时，由于编码风格不同引起的不便。它通过列举一组规定和期望如何格式化PHP代码来实现。

本篇中的代码风格规则源于各个成员项目之间的共性。当各个作者跨多个项目进行协作时，在所有这些项目中使用一套规范会很有帮助。因此，本规范的好处不在于规则本身，而在于共享这些规则。

关键字：必须(MUST)，一定不可(MUST NOT)，需要(REQUIRED)，将会(SHALL)，不会(SHALL NOT)，应该(SHOULD)，不应该(SHOULD NOT)，推荐(RECOMMENDED)，可以(MAY)，可选(OPTIONAL) 可以在 [RFC 2119] 找到详细描述。

[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[PSR-1]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md

# 1. 概述

- 代码必须遵循“编码风格指南”PSR [[PSR-1]]。
- 代码必须使用4个空格进行缩进，而不是制表符。
- 每行的长度一定不可有硬性限制，软性限制必须是120个字符，应该保持在80个字符以内。
- 在命名空间声明 `namespace` 之后必须有一个空行，在 `use` 块之后必须有一个空行。
- 类的左大括号必须在下一行，右大括号必须在主体的下一行。
- 方法的左大括号必须在方法声明的下一行，右大括号必须在方法主体的下一行。
- 必须在所有属性和方法上声明可见性；`abstract` 和 `final` 关键字必须在可见性关键字之前；`static` 关键字必须在可见性关键字之后。
- 流程控制关键字之后必须有一个空格，方法和函数之后一定不可有空格。
- 流程控制的左大括号必须在同一行，右大括号必须在主体的下一行。
- 流程控制的左小括号之后一定不可有空格，右小括号之前一定不可有空格。


## 1.1. 例子

下面这个包含一些规则的例子，可以作为快速概览：

~~~php
<?php
namespace Vendor\Package;

use FooInterface;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class Foo extends Bar implements FooInterface
{
    public function sampleMethod($a, $b = null)
    {
        if ($a === $b) {
            bar();
        } elseif ($a > $b) {
            $foo->bar($arg1);
        } else {
            BazClass::bar($arg2, $arg3);
        }
    }

    final public static function bar()
    {
        // method body
    }
}
~~~

## 2. 通则

### 2.1. 基本编码标准

代码必须遵循 [PSR-1] 中列出的所有规则。

### 2.2. 文件

所有PHP文件必须使用Unix LF（换行）行结尾。

所有PHP文件必须以一个空行结束。

在只包含PHP代码的文件中，必须省略结束标签 `?>`。

### 2.3. 行

行长度一定不可有硬性限制。

软性限制必须是120个字符，在这个限制上，自动代码风格检查器必须发出警告，但一定不可报错。

行长度不应该超过80个字符，超长的行应该被拆分成一些不超过80个字符长的行。

在非空白行的结尾，一定不可有空格。

可以添加空行以提高可读性，表示指示相关的代码块。

每行一定不可多于一个语句。

### 2.4. 缩进

代码必须使用4个空格的缩进，并且一定不可使用制表符进行缩进。

> 注意：仅使用空格，而不使用制表符，有助于避免diffs，patches，history和注释的问题。空格还有助于更轻松的使用子缩进的对齐。

### 2.5. 关键字 和 True/False/Null

PHP[关键字]必须使用小写。

PHP常量 `true`， `false` 和 `null`必须使用小写。

[关键字]: http://php.net/manual/en/reserved.keywords.php

## 3. namespace 和 use 声明

如果存在命名空间`namespace`声明，则在其后必须有一个空行。

如果存在，所有使用声明`use`必须在命名空间声明`namespace`之后。

每个声明必须有一个`use`关键字。

使用`use`块后必须有一个空行。

例如：

~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

// ... additional PHP code ...

~~~

## 4. 类，属性 和 方法

术语“类”指的是所有类(classes)，接口(interfaces)和特征(traits)。

### 4.1. 继承和实现

`extends`和`implements`关键字必须在类名称的同一行声明。

类的左大括号必须独占一行，右大括号必须在主体之后独占一行。

~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements \ArrayAccess, \Countable
{
    // constants, properties, methods
}
~~~

`implements` 的列表可以拆分为多行，从第二行开始，每行缩进一次。这样做时，第一个接口必须在下一行，并且每行必须只有一个接口。

~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements
    \ArrayAccess,
    \Countable,
    \Serializable
{
    // constants, properties, methods
}
~~~

### 4.2. 属性

必须在所有属性上声明可见性。

`var`关键字一定不可用于声明属性。

每个语句一定不可声明超过一个属性。

属性名称不应以单个下划线为前缀，来表示受保护或私有的可见性。

属性声明如下所示。

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public $foo = null;
}
~~~

### 4.3. 方法

必须在所有方法上声明可见性。

方法名称不应以单个下划线为前缀，以指示受保护或私有的可见性。

方法名称之后一定不可有空格。左大括号必须独自占用一行，右大括号必须在主体之后独自占用一行。左小括号之后一定不可有空格，右小括号之前一定不可有空格。

方法声明如下所示。请注意小括号，逗号，空格和大括号的位置：

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function fooBarBaz($arg1, &$arg2, $arg3 = [])
    {
        // method body
    }
}
~~~

### 4.4. 方法参数

在参数列表中，每个逗号前一定不可有空格，每个逗号后必须有一个空格。

具有默认值的参数必须位于参数列表的末尾。

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function foo($arg1, &$arg2, $arg3 = [])
    {
        // method body
    }
}
~~~

参数列表可以分为多行，每行都缩进一次。这样做时，列表中的第一项必须在下一行，每行必须只有一个参数。

当参数列表分成多行时，右小括号和左大括号必须放在一起，它们之间有一个空格。

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function aVeryLongMethodName(
        ClassTypeHint $arg1,
        &$arg2,
        array $arg3 = []
    ) {
        // method body
    }
}
~~~

### 4.5.  `abstract`， `final` 和 `static`

如果存在，抽象`abstract`和最终`final`声明必须在可见性声明之前。

如果存在，静态`static`声明必须在可见性声明之后。

~~~php
<?php
namespace Vendor\Package;

abstract class ClassName
{
    protected static $foo;

    abstract protected function zim();

    final public static function bar()
    {
        // method body
    }
}
~~~

### 4.6. 方法和函数调用

当调用方法或函数时，在方法名和左括号之间一定不可有空格，在左括号之后一定不可有空格，在右括号之前一定不可有空格。在参数列表中，每个逗号前一定不可有空格，每个逗号后必须有一个空格。

~~~php
<?php
bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
~~~

参数列表可以分为多行，后续行每行缩进一次。这样做时，列表中的第一项必须在下一行，并且每行必须只有一个参数。

~~~php
<?php
$foo->bar(
    $longArgument,
    $longerArgument,
    $muchLongerArgument
);
~~~

## 5. 控制结构

控制结构的一般风格规则如下：

- 控制结构关键字后面必须有一个空格
- 在左括号后面不能有空格
- 在右括号之前不能有空格
- 在右括号和左大括号之间必须有一个空格
- 结构体必须缩进一次
- 右大括号必须在主体之后独占一行

每个结构的主体必须用括号括起来。这样结构体看起来更加标准，并且可以减少在给主体增加新行时引起错误的可能性。

### 5.1. `if`, `elseif`, `else`

`if`结构如下所示。注意括号，空格和大括号的位置；`else`和`elseif`与上一个主体的右括号位于同一行上。

~~~php
<?php
if ($expr1) {
    // if body
} elseif ($expr2) {
    // elseif body
} else {
    // else body;
}
~~~

`else if`应该使用关键字`elseif`替代，这样可以使控制关键字看起来更像一个单词。

### 5.2.  `switch`, `case`

`switch`结构如下所示。请注意括号，空格和大括号的位置。`case`语句必须相对`switch`缩进一次，`break`关键字（或其他终止关键字）必须相对`case`缩进一次。如果存在包含主体并且没有终止关键字的`case`，则必须包含类似`// no break`的注释。

~~~php
<?php
switch ($expr) {
    case 0:
        echo 'First case, with a break';
        break;
    case 1:
        echo 'Second case, which falls through';
        // no break
    case 2:
    case 3:
    case 4:
        echo 'Third case, return instead of break';
        return;
    default:
        echo 'Default case';
        break;
}
~~~

### 5.3. `while` 和 `do while`

`while`语句如下所示。请注意括号，空格和大括号的位置。

~~~php
<?php
while ($expr) {
    // structure body
}
~~~

类似地，`do while`语句如下所示。请注意括号，空格和大括号的位置。

~~~php
<?php
do {
    // structure body;
} while ($expr);
~~~

### 5.4. `for`

`for`语句如下所示。请注意括号，空格和大括号的位置。

~~~php
<?php
for ($i = 0; $i < 10; $i++) {
    // for body
}
~~~

### 5.5. `foreach`

`foreach`语句如下所示。请注意括号，空格和大括号的位置。

~~~php
<?php
foreach ($iterable as $key => $value) {
    // foreach body
}
~~~

### 5.6. `try` 和 `catch`

`try catch`块如下所示。请注意括号，空格和大括号的位置。

~~~php
<?php
try {
    // try body
} catch (FirstExceptionType $e) {
    // catch body
} catch (OtherExceptionType $e) {
    // catch body
}
~~~

## 6. 闭包

声明闭包时，关键字`function`之后必须有一个空格，`use` 关键字的前后都必须有一个空格。

左大括号必须和声明在同一行，右大括号必须在主体之后的下一行。

参数列表或变量列表的左括号后面一定不可有空格，并且在参数列表或变量列表的右括号之前一定不可有空格。

在参数列表和变量列表中，每个逗号前一定不可有空格，每个逗号后必须有一个空格。

具有默认值的闭包参数必须位于参数列表的末尾。

闭包声明如下所示。请注意括号，逗号，空格和大括号的位置：

~~~php
<?php
$closureWithArgs = function ($arg1, $arg2) {
    // body
};

$closureWithArgsAndVars = function ($arg1, $arg2) use ($var1, $var2) {
    // body
};
~~~

参数列表和变量列表可以分为多行，后续每行缩进一次。这样做时，列表中的第一项必须在下一行，并且每行必须只有一个参数或变量。

当结束列表（无论是参数还是变量）被分割成多行时，右括号和左大括号必须放在一起，并且它们之间有一个空格。

以下是包含和不包含参数列表的闭包的示例，以及跨多行分割的变量列表示例。

~~~php
<?php
$longArgs_noVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) {
    // body
};

$noArgs_longVars = function () use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // body
};

$longArgs_longVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // body
};

$longArgs_shortVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use ($var1) {
    // body
};

$shortArgs_longVars = function ($arg) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // body
};
~~~

请注意，当闭包直接作为函数或方法调用中的参数时，格式设置规则也适用。

~~~php
<?php
$foo->bar(
    $arg1,
    function ($arg2) use ($var1) {
        // body
    },
    $arg3
);
~~~

## 7. 结论

本规范有意省略了许多风格和实践元素，包括但不限于：

- 全局变量和全局常量的声明
- 函数的声明
- 操作符和赋值
- 行内对齐
- 注释和文档块
- 类名的前缀和后缀
- 最佳实践

未来本篇规范可能会修订和扩展，以解决编码风格和实践中的这些和其他元素。

## 附录 A. 调查

在撰写本篇风格规范时，小组对成员项目进行了调查以确定常见做法。保留在此以供查阅。

调查数据原文：[[PSR-1]]
