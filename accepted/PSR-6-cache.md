# 缓存接口

缓存是提高项目性能的常用方法，这使缓存库成为许多框架和库的最常见功能之一。这导致许多库推出自己的缓存库，具有各种功能。这些差异导致开发人员必须学习多个系统，这些系统可能会也可能不会提供他们所需的功能。此外，缓存库本身的开发人员面临着只支持有限数量的框架或创建大量适配器类的选择。

一个缓存系统的通用接口将解决这些问题。库和框架开发人员可以按照他们期望的方式依赖缓存系统，而缓存系统的开发人员只需要实现一组接口而不是各种各样的适配器。

关键字：必须(MUST)，一定不可(MUST NOT)，需要(REQUIRED)，将会(SHALL)，不会(SHALL NOT)，应该(SHOULD)，不应该(SHOULD NOT)，推荐(RECOMMENDED)，可以(MAY)，可选(OPTIONAL) 可以在 [RFC 2119](http://tools.ietf.org/html/rfc2119) 找到详细描述。

## 目标

此PSR的目标是允许开发人员创建可以集成到现有框架和系统中的缓存库，并且系统无需对缓存库定制开发。

## 定义

- 调用库 - 实际需要缓存服务的库或代码。该库将利用实现此标准接口的缓存服务，但不会知道这些缓存服务的实现。

- 实现库 - 该库负责实现此标准，以便为任何调用库提供缓存服务。实现库必须提供实现`Cache\CacheItemPoolInterface`和`Cache\CacheItemInterface`接口的类。实现库必须支持如下所述的TTL功能。

- TTL - 项目的生存时间（TTL）是该项目存储与失效之间的时间量。TTL通常由表示以秒为单位的时间的整数或`DateInterval`对象定义。

- 过期时间 - 项目失效的实际时间。这通常通过将TTL添加到存储对象的时间来计算，但也可以使用DateTime对象显式设置。例如在1:30:00存储300秒TTL的项目，到期时间为1:35:00。实现库可以在其请求的到期时间之前使项目过期，但是一旦达到其到期时间，必须将项目视为已过期。如果调用库要求保存项目但未指定过期时间，或指定空过期时间或TTL，则实现库可以使用配置的默认持续时间。如果没有设置默认持续时间，则实现库必须将其解释为永久缓存项目的请求，或者底层实现支持的最长时间。

- 键 - 至少一个字符的字符串，用于唯一标识缓存的项目。实现库必须支持由字符`A-Z`，`a-z`，`0-9`，`_`和`.`组成的键。以UTF-8编码的任何顺序排列，长度最多为64个字符。实现库可以支持其他字符和编码或更长的长度，但必须至少支持该最小值。库负责自己的密钥字符串转义，但必须能够返回原始未修改的密钥字符串。以下字符保留用于将来的扩展，并且实现库一定不可支持：`{}()/\@:`

- 命中 - 当调用库按键请求项目，能找到该键的匹配值并且该值尚未过期时，而且也没有因为其他原因而失效时，会发生缓存命中。调用库应该确保在所有`get()`调用上验证`isHit()`。

- 未命中 - 缓存未命中与缓存命中相反。当调用库按键请求项目并且找不到该键的值，或者找到但已过期的值，或者由于某些其他原因该值无效时，会发生缓存未命中。过期的值必须始终被视为缓存未命中。

- 延迟缓存 - 延迟缓存保存表示缓存池可能不会立即保留缓存项。缓存池对象可以延迟持久化延迟缓存项目，以便利用某些存储引擎支持的批量设置操作。缓存池必须确保最终持久化任何延迟缓存项并且数据不会丢失，并且可以在调用库请求它们被持久化之前保留它们。当调用库调用`commit()`方法时，所有未被持久化的延迟项必须被持久化。实现库可以自己决定逻辑来确定何时持久化延迟项，例如在析构函数里持久化，在`save()`函数里持久化，在超时或超过最大缓存项时持久化，或任何其他适当的逻辑。请求一个延迟缓存项，必须返回已经延迟但是未被持久化的值。

## 数据

实现库必须支持所有可序列化的PHP数据类型，包括：

- Strings - 任何PHP兼容编码中任意长度的字符串。
- Integers - PHP支持的任何大小的所有整数，最多64位签名。
- Floats - 所有已签名的浮点值。
- Boolean - `True` 和 `False`.
- Null - 实际的空值。
- Arrays - 任意深度的索引，关联和多维数组。
- Object - 任何支持无损序列化和反序列化的对象，例如`$o == unserialize(serialize($o))`。对象可以使用PHP的`Serializable`接口，`__sleep()`或`__wakeup()`魔术方法，或者适当的类似语言功能。

传递到实现库的所有数据必须完全按照传递的方式返回。包括变量类型。也就是说，返回`（string）5`是错误的，如果`（int）5`是保存的值。实现库可以在内部使用PHP的`serialize()`/`unserialize()`函数，但不是必须这样做。使用它们是一个可以接受对象参数的简单的方式。

## 关键概念

### 缓存池(Pool)

缓存池表示缓存系统中的项目集合。缓存池是它包含的所有项的逻辑存储库。所有可缓存的项都作为Item对象从缓存池中检索，并且所有与缓存对象的全部交互都通过缓存池进行。

### 缓存项(Items)

Item表示池中的单个键/值对。键是Item的主要唯一标识符，必须是不可变的。值可以随时更改。

## 错误处理

虽然缓存通常是应用程序性能的重要组成部分，但它永远不应成为应用程序功能的关键部分。所以，缓存系统中的错误不应导致应用程序失败。因此，实现库一定不可抛出除接口定义的异常之外的异常，并且应该捕获由底层数据存储触发的任何错误或异常，并且不允许它们向上冒泡。

实现库应该记录这些错误或以适当方式将其报告给管理员。

当调用库请求的一个或多个项已经被删除，或者缓存池已经被清空，也就是说，请求的键不存在时，一定不可将其视为错误。后置条件相同（键不存在，或者缓存池为空），因此没有错误条件。

## 接口

### CacheItemInterface

`CacheItemInterface`定义缓存系统内的项。每个`Item`对象必须与一个特定的键相关联，该键可以根据实现系统进行设置，并且通常由`Cache\CacheItemPoolInterface`对象传递。

`Cache\CacheItemInterface`对象封装了缓存项的存储和检索。每个`Cache\CacheItemInterface`都由`Cache\CacheItemPoolInterface`对象生成，该对象负责任何所需的设置以及将对象与唯一的键相关联。 `Cache\CacheItemInterface`对象必须能够存储和检索本文档“数据”部分中定义的任何类型的PHP值。

调用库一定不可自己实例化Item对象。它们只能通过`getItem()`方法从`Pool`对象请求。调用库不应该假设一个实现库创建的项与另一个实现库兼容。

```php
<?php

namespace Psr\Cache;

/**
 * CacheItemInterface defines an interface for interacting with objects inside a cache.
 */
interface CacheItemInterface
{
    /**
     * Returns the key for the current cache item.
     *
     * The key is loaded by the Implementing Library, but should be available to
     * the higher level callers when needed.
     *
     * @return string
     *   The key string for this cache item.
     */
    public function getKey();

    /**
     * Retrieves the value of the item from the cache associated with this object's key.
     *
     * The value returned must be identical to the value originally stored by set().
     *
     * If isHit() returns false, this method MUST return null. Note that null
     * is a legitimate cached value, so the isHit() method SHOULD be used to
     * differentiate between "null value was found" and "no value was found."
     *
     * @return mixed
     *   The value corresponding to this cache item's key, or null if not found.
     */
    public function get();

    /**
     * Confirms if the cache item lookup resulted in a cache hit.
     *
     * Note: This method MUST NOT have a race condition between calling isHit()
     * and calling get().
     *
     * @return bool
     *   True if the request resulted in a cache hit. False otherwise.
     */
    public function isHit();

    /**
     * Sets the value represented by this cache item.
     *
     * The $value argument may be any item that can be serialized by PHP,
     * although the method of serialization is left up to the Implementing
     * Library.
     *
     * @param mixed $value
     *   The serializable value to be stored.
     *
     * @return static
     *   The invoked object.
     */
    public function set($value);

    /**
     * Sets the expiration time for this cache item.
     *
     * @param \DateTimeInterface|null $expiration
     *   The point in time after which the item MUST be considered expired.
     *   If null is passed explicitly, a default value MAY be used. If none is set,
     *   the value should be stored permanently or for as long as the
     *   implementation allows.
     *
     * @return static
     *   The called object.
     */
    public function expiresAt($expiration);

    /**
     * Sets the expiration time for this cache item.
     *
     * @param int|\DateInterval|null $time
     *   The period of time from the present after which the item MUST be considered
     *   expired. An integer parameter is understood to be the time in seconds until
     *   expiration. If null is passed explicitly, a default value MAY be used.
     *   If none is set, the value should be stored permanently or for as long as the
     *   implementation allows.
     *
     * @return static
     *   The called object.
     */
    public function expiresAfter($time);

}
```

## CacheItemPoolInterface

`Cache\CacheItemPoolInterface`的主要目的是接受来自调用库的键并返回关联的`Cache\CacheItemInterface`对象。它也是与整个缓存集合交互的主要点。缓存池的所有配置和初始化都由实现库决定。

```php
<?php

namespace Psr\Cache;

/**
 * CacheItemPoolInterface generates CacheItemInterface objects.
 */
interface CacheItemPoolInterface
{
    /**
     * Returns a Cache Item representing the specified key.
     *
     * This method must always return a CacheItemInterface object, even in case of
     * a cache miss. It MUST NOT return null.
     *
     * @param string $key
     *   The key for which to return the corresponding Cache Item.
     *
     * @throws InvalidArgumentException
     *   If the $key string is not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return CacheItemInterface
     *   The corresponding Cache Item.
     */
    public function getItem($key);

    /**
     * Returns a traversable set of cache items.
     *
     * @param string[] $keys
     *   An indexed array of keys of items to retrieve.
     *
     * @throws InvalidArgumentException
     *   If any of the keys in $keys are not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return array|\Traversable
     *   A traversable collection of Cache Items keyed by the cache keys of
     *   each item. A Cache item will be returned for each key, even if that
     *   key is not found. However, if no keys are specified then an empty
     *   traversable MUST be returned instead.
     */
    public function getItems(array $keys = array());

    /**
     * Confirms if the cache contains specified cache item.
     *
     * Note: This method MAY avoid retrieving the cached value for performance reasons.
     * This could result in a race condition with CacheItemInterface::get(). To avoid
     * such situation use CacheItemInterface::isHit() instead.
     *
     * @param string $key
     *   The key for which to check existence.
     *
     * @throws InvalidArgumentException
     *   If the $key string is not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return bool
     *   True if item exists in the cache, false otherwise.
     */
    public function hasItem($key);

    /**
     * Deletes all items in the pool.
     *
     * @return bool
     *   True if the pool was successfully cleared. False if there was an error.
     */
    public function clear();

    /**
     * Removes the item from the pool.
     *
     * @param string $key
     *   The key to delete.
     *
     * @throws InvalidArgumentException
     *   If the $key string is not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return bool
     *   True if the item was successfully removed. False if there was an error.
     */
    public function deleteItem($key);

    /**
     * Removes multiple items from the pool.
     *
     * @param string[] $keys
     *   An array of keys that should be removed from the pool.

     * @throws InvalidArgumentException
     *   If any of the keys in $keys are not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return bool
     *   True if the items were successfully removed. False if there was an error.
     */
    public function deleteItems(array $keys);

    /**
     * Persists a cache item immediately.
     *
     * @param CacheItemInterface $item
     *   The cache item to save.
     *
     * @return bool
     *   True if the item was successfully persisted. False if there was an error.
     */
    public function save(CacheItemInterface $item);

    /**
     * Sets a cache item to be persisted later.
     *
     * @param CacheItemInterface $item
     *   The cache item to save.
     *
     * @return bool
     *   False if the item could not be queued or if a commit was attempted and failed. True otherwise.
     */
    public function saveDeferred(CacheItemInterface $item);

    /**
     * Persists any deferred cache items.
     *
     * @return bool
     *   True if all not-yet-saved items were successfully saved or there were none. False otherwise.
     */
    public function commit();
}
```

## CacheException

此异常接口旨在用于发生严重错误时抛出，包括但不限于缓存设置，如连接到缓存服务器或提供的凭据无效。

实现库抛出的任何异常都必须实现此接口。

```php
<?php

namespace Psr\Cache;

/**
 * Exception interface for all exceptions thrown by an Implementing Library.
 */
interface CacheException
{
}
```

## InvalidArgumentException

```php
<?php

namespace Psr\Cache;

/**
 * Exception interface for invalid cache arguments.
 *
 * Any time an invalid argument is passed into a method it must throw an
 * exception class which implements Psr\Cache\InvalidArgumentException.
 */
interface InvalidArgumentException extends CacheException
{
}
```
