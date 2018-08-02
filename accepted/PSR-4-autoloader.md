# 自动加载器

关键字：必须(MUST)，一定不可(MUST NOT)，需要(REQUIRED)，将会(SHALL)，不会(SHALL NOT)，应该(SHOULD)，不应该(SHOULD NOT)，推荐(RECOMMENDED)，可以(MAY)，可选(OPTIONAL) 可以在 [RFC 2119](http://tools.ietf.org/html/rfc2119) 找到详细描述。

## 1. 概述

此PSR描述了从文件路径[自动加载]类的规范。它是完全可互操作的，可以用于补充任何其它自动加载规范，包括 [PSR-0]。此PSR还描述了，根据规范哪些目录的文件将被自动加载。

## 2. 规范

1. 术语“类”指的是类，接口，特征和其他类似结构。

2. 一个完整的类名如下所示

        \<NamespaceName>(\<SubNamespaceNames>)*\<ClassName>

    1. 完整的类名必须有一个顶级的命名空间，也称为“供应商(vendor)命名空间”。
    
    2. 完整的类名可以有一个或多个子命名空间。
    
    3. 完整的类名必须有一个终止的类名。
    
    4. 下划线在完整类名的任何部分都没有特殊含义。
    
    5. 完整类名中的字母字符可以是小写和大写的任意组合。
    
    6. 必须以区分大小写的方式引用所有类名。

3. 加载对应于完整类名的文件时...

    1. 一系列连续的一个或多个主命名空间和子命名空间，不包括主命名空间的分隔符，在完整类名（命名空间前缀）中，对应至少一个“基础目录”。
    
    2. “命名空间前缀”之后的连续子命名空间名称对应于“基础目录”中的子目录，其中名称空间分隔符表示目录分隔符。子目录名称必须与子命名空间名称的大小写匹配。
    
    3. 终止类名对应于以`.php`结尾的文件名。文件名必须与终止类名称的大小写相匹配。
    
4. 自动加载器的实现，一定不可抛出任何异常，一定不可引发任何级别的错误，也不应该有返回值。

## 3. 例子

下表给出了对应的完整类名，命名空间前缀，基础目录和文件路径。

| 完整类名    | 命名空间前缀   | 基础目录           | 文件路径
| ----------------------------- |--------------------|--------------------------|-------------------------------------------
| \Acme\Log\Writer\File_Writer  | Acme\Log\Writer    | ./acme-log-writer/lib/   | ./acme-log-writer/lib/File_Writer.php
| \Aura\Web\Response\Status     | Aura\Web           | /path/to/aura-web/src/   | /path/to/aura-web/src/Response/Status.php
| \Symfony\Core\Request         | Symfony\Core       | ./vendor/Symfony/Core/   | ./vendor/Symfony/Core/Request.php
| \Zend\Acl                     | Zend               | /usr/includes/Zend/      | /usr/includes/Zend/Acl.php

有关符合规范的自动加载器的实现，请参阅[示例文件]。示例实现一定不可视为规范的一部分，并且可以随时更改。


[自动加载]: http://php.net/autoload
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[示例文件]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader-examples.md
