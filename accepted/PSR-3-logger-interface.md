# 日志接口

本文档描述了一个用于日志库的通用接口。

主要目标是允许各种库以简单和通用的方式接收`Psr\Log\LoggerInterface`对象并将日志写入其中。具有自定义需求的框架和CMS可以扩展接口以用于它们自己的目的，但是应该保持与本文档的兼容性。这可确保应用程序使用的第三方库可以写入集中式应用程序日志。

关键字：必须(MUST)，一定不可(MUST NOT)，需要(REQUIRED)，将会(SHALL)，不会(SHALL NOT)，应该(SHOULD)，不应该(SHOULD NOT)，推荐(RECOMMENDED)，可以(MAY)，可选(OPTIONAL) 可以在 [RFC 2119] 找到详细描述。

本文档中的`实现者`是指实现了`LoggerInterface`的库或框架，日志的使用者被称为`用户`。

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 1. 规范

### 1.1. 基础

- `LoggerInterface` 声明了八个方法用来记录 [RFC 5424] 中提出了八个日志级别（debug, info, notice, warning, error, critical, alert, emergency)。
- 第九种方法`log`接受日志级别作为第一个参数。使用其中一个日志级别常量调用此方法必须与调用特定级别的方法具有相同的结果。如果调用了本规范之外的级别，方法中也未处理，则必须抛出`Psr\Log\InvalidArgumentException`异常。用户不应该在不知道当前实现是否支持的情况下使用自定义级别。

[RFC 5424]: http://tools.ietf.org/html/rfc5424

### 1.2. 信息参数

- 每个方法都接受一个字符串作为消息，或一个实现了`__toString()`方法的对象。实现者可以对传递的对象进行特殊处理。如果不是这样，实现者必须将其转换为字符串。

- 消息可以包含占位符，实现者可以使用上下文数组中的值进行替换。

占位符名称必须对应于上下文数组中的键。

占位符名称必须用一个左括号`{`和一个右括号`}`分隔。分隔符和占位符名称之间不能有任何空格。

占位符名称应该仅由字符`A-Z`，`a-z`，`0-9`，下划线`_`和句点`.`组成。其他字符的保留以备用于将来修改占位符规范。

实现者可以使用占位符来实现各种转义策略并转换日志以供显示。用户不应该预先转义占位符值，因为他们无法知道数据将在哪个上下文中显示。

以下是占位符插值的示例实现，仅供参考：

```php
<?php

/**
 * Interpolates context values into the message placeholders.
 */
function interpolate($message, array $context = array())
{
    // build a replacement array with braces around the context keys
    $replace = array();
    foreach ($context as $key => $val) {
        // check that the value can be casted to string
        if (!is_array($val) && (!is_object($val) || method_exists($val, '__toString'))) {
            $replace['{' . $key . '}'] = $val;
        }
    }

    // interpolate replacement values into the message and return
    return strtr($message, $replace);
}

// a message with brace-delimited placeholder names
$message = "User {username} created";

// a context array of placeholder names => replacement values
$context = array('username' => 'bolivar');

// echoes "User bolivar created"
echo interpolate($message, $context);
```

### 1.3. 上下文参数

- 每个方法都接受一个数组作为上下文数据。这意味着保存任何不适合转为字符串的无关信息。实现者必须确保他们尽可能地对待上下文数据。上下文中的给定值绝不能抛出异常，也不会引发任何php错误，警告或通知。
- 如果在上下文数据中传递了`Exception`对象，则它必须位于`“exception”`键中。记录异常是一种常见模式，这允许实现者在日志后端支持时从异常中提取堆栈跟踪。在使用它之前，实现者仍然必须验证`'exception'`键的值确实是一个`Exception`对象，因为它可能包含任何东西。

### 1.4. 帮助类和接口

- 通过继承`Psr\Log\AbstractLogger`类，可以轻松的实现`LoggerInterface`接口，然后实现`Log`方法。其他八种方法是将消息和上下文转发给它。
- 同样，使用`Psr\Log\LoggerTrait`只需要实现通用`Log`方法。请注意，由于traits无法实现接口，因此在这种情况下仍需要实现`LoggerInterface`。
- `Psr\Log\NullLogger`与接口一起提供。当用户没有记录器的时候，它可以给用户当一个黑洞。但是，如果上下文数据创建很消耗资源，则根据条件记录可能是更好的方法。
- `Psr\Log\LoggerAwareInterface`只包含一个`setLogger(LoggerInterface $logger)`方法，框架可以用该方法任意连接日志记录器。
- `Psr\Log\LoggerAwareTrait`特性可用于在任何类中轻松实现等效接口。它允许您访问`$this->logger`。
- `Psr\Log\LogLevel`类保存八个日志级别的常量。

## 2. 包

描述的接口和类以及相关的异常类和用于验证实现的测试套件是作为 [psr/log](https://packagist.org/packages/psr/log) 包的一部分提供的。

## 3. `Psr\Log\LoggerInterface`

```php
<?php

namespace Psr\Log;

/**
 * Describes a logger instance
 *
 * The message MUST be a string or object implementing __toString().
 *
 * The message MAY contain placeholders in the form: {foo} where foo
 * will be replaced by the context data in key "foo".
 *
 * The context array can contain arbitrary data, the only assumption that
 * can be made by implementors is that if an Exception instance is given
 * to produce a stack trace, it MUST be in a key named "exception".
 *
 * See https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md
 * for the full interface specification.
 */
interface LoggerInterface
{
    /**
     * System is unusable.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function emergency($message, array $context = array());

    /**
     * Action must be taken immediately.
     *
     * Example: Entire website down, database unavailable, etc. This should
     * trigger the SMS alerts and wake you up.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function alert($message, array $context = array());

    /**
     * Critical conditions.
     *
     * Example: Application component unavailable, unexpected exception.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function critical($message, array $context = array());

    /**
     * Runtime errors that do not require immediate action but should typically
     * be logged and monitored.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function error($message, array $context = array());

    /**
     * Exceptional occurrences that are not errors.
     *
     * Example: Use of deprecated APIs, poor use of an API, undesirable things
     * that are not necessarily wrong.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function warning($message, array $context = array());

    /**
     * Normal but significant events.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function notice($message, array $context = array());

    /**
     * Interesting events.
     *
     * Example: User logs in, SQL logs.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function info($message, array $context = array());

    /**
     * Detailed debug information.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function debug($message, array $context = array());

    /**
     * Logs with an arbitrary level.
     *
     * @param mixed $level
     * @param string $message
     * @param array $context
     * @return void
     */
    public function log($level, $message, array $context = array());
}
```

## 4. `Psr\Log\LoggerAwareInterface`

~~~php
<?php

namespace Psr\Log;

/**
 * Describes a logger-aware instance
 */
interface LoggerAwareInterface
{
    /**
     * Sets a logger instance on the object
     *
     * @param LoggerInterface $logger
     * @return void
     */
    public function setLogger(LoggerInterface $logger);
}
~~~

## 5. `Psr\Log\LogLevel`

~~~php
<?php

namespace Psr\Log;

/**
 * Describes log levels
 */
class LogLevel
{
    const EMERGENCY = 'emergency';
    const ALERT     = 'alert';
    const CRITICAL  = 'critical';
    const ERROR     = 'error';
    const WARNING   = 'warning';
    const NOTICE    = 'notice';
    const INFO      = 'info';
    const DEBUG     = 'debug';
}
~~~
