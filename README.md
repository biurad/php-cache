# The Biurad PHP Cache

[![Latest Version](https://img.shields.io/packagist/v/biurad/cache.svg?style=flat-square)](https://packagist.org/packages/biurad/cache)
[![Software License](https://img.shields.io/badge/License-BSD--3-brightgreen.svg?style=flat-square)](LICENSE)
[![Workflow Status](https://img.shields.io/github/workflow/status/biurad/php-cache/Tests?style=flat-square)](https://github.com/biurad/php-cache/actions?query=workflow%3ATests)
[![Code Maintainability](https://img.shields.io/codeclimate/maintainability/biurad/php-cache?style=flat-square)](https://codeclimate.com/github/biurad/php-cache)
[![Coverage Status](https://img.shields.io/codecov/c/github/biurad/php-cache?style=flat-square)](https://codecov.io/gh/biurad/php-cache)
[![Quality Score](https://img.shields.io/scrutinizer/g/biurad/php-cache.svg?style=flat-square)](https://scrutinizer-ci.com/g/biurad/php-cache)
[![Sponsor development of this project](https://img.shields.io/badge/sponsor%20this%20package-%E2%9D%A4-ff69b4.svg?style=flat-square)](https://biurad.com/sponsor)

**biurad/php-cache** is a php cache library based on [PSR-6] and [PSR-16][] which should supports many different drivers such as redis, memcache, apc, mongodb and others.

## 📦 Installation & Basic Usage

This project requires [PHP] 7.1 or higher. The recommended way to install, is via [Composer]. Simply run:

```bash
$ composer require biurad/cache
```

This library comes in handy with support for the soon to be deprecated [Doctrine Cache][], while [PSR-16] requires [PSR-6].

```php
// you can use any of doctrine cache adapter
$storage = new Biurad\Cache\AdapterFactory::createHandler('array');
// or
$storage = new Doctrine\Common\Cache\ArrayCache();
```

Most methods in `BiuradPHP\Cache\FastCache` class that has a second parameter as `callable`, which is called when there is no such item in the cache. This callback receives 1 argument, which is an instance of `Psr\Cache\CacheItemInterface`.

```php
use BiuradPHP\Cache\CacheItemPool;
use BiuradPHP\Cache\SimpleCache;
use BiuradPHP\Cache\FastCache;

// you can use any of doctrine cache adapter
$adapter = AdapterFactory::createHandler('redis://localhost');

$cache = new FastCache($adapter);
```

The first argument of the `load()` method is a key, an arbitrary string that you associate to the cached value so you can retrieve it later. The second argument is a PHP callable which is executed when the key is not found in the cache to generate and return the value:

```php
use Psr\Cache\CacheItemInterface;

// The callable will only be executed on a cache miss.
$value = $cache->load('my_cache_key', static function (CacheItemInterface $item) {
    $item->expiresAfter(3600);

    // ... do some HTTP request or heavy computations
    $item->set('foobar');

    return $item;
});

echo $value; // 'foobar'

// ... and to remove the cache key
$cache->delete('my_cache_key');
```

The callable caching feature. Caching the result of a function or method call can be achieved using the `call()` method:

```php
$name = $cache->call('gethostbyaddr', $ip);
```

The `gethostbyaddr($ip)` will, therefore, be called only once and next time, only the value from cache will be returned. Of course, for different `$ip`, different results are cached. But if you want to set expiry time on call, add `Psr\Cache\CacheItemInterface` argument at the end and set the expiration time.

Similarly, it is possible to wrap a function with cache and call it later.

```php
function calculate($number)
{
	return 'number is ' . $number;
}

$wrapper = $cache->wrap('calculate');

$result = $wrapper(1); // number is 1
$result = $wrapper(2); // number is 2
```

The template/output caching feature. Caching the result of an output can be cached not only in templates:

```php
if ($block = $cache->start($key)) {
	... printing some data ...

	$block->end(); // save the output to the cache
}
```

In case that the output is already present in the cache, the `start()` method prints it and returns `null`. Otherwise, it starts to buffer the output and returns the `$block` object using which we finally save the data to the cache.

The expiration and invalidation caching feature.

This feature works with only PSR-6 cache, By default the beta is 1.0 and higher values mean earlier recompute. Set it to 0 to disable early recompute and set it to INF to force an immediate recompute:

```php
use Psr\Cache\CacheItemInterface;

$beta = 1.0;
$value = $cache->save('my_cache_key', static function (CacheItemInterface $item) {
    $item->expiresAfter(3600);

    return $item;
}, $beta);
```

## 📓 Documentation

For in-depth documentation before using this library.. Full documentation on advanced usage, configuration, and customization can be found at [docs.biurad.com][docs].

## ⏫ Upgrading

Information on how to upgrade to newer versions of this library can be found in the [UPGRADE].

## 🏷️ Changelog

[SemVer](http://semver.org/) is followed closely. Minor and patch releases should not introduce breaking changes to the codebase; See [CHANGELOG] for more information on what has changed recently.

Any classes or methods marked `@internal` are not intended for use outside of this library and are subject to breaking changes at any time, so please avoid using them.

## 🛠️ Maintenance & Support

When a new **major** version is released (`1.0`, `2.0`, etc), the previous one (`0.19.x`) will receive bug fixes for _at least_ 3 months and security updates for 6 months after that new release comes out.

(This policy may change in the future and exceptions may be made on a case-by-case basis.)

**Professional support, including notification of new releases and security updates, is available at [Biurad Commits][commit].**

## 👷‍♀️ Contributing

To report a security vulnerability, please use the [Biurad Security](https://security.biurad.com). We will coordinate the fix and eventually commit the solution in this project.

Contributions to this library are **welcome**, especially ones that:

- Improve usability or flexibility without compromising our ability to adhere to [PSR-6] and [PSR-16]
- Mirror fixes made to the [Doctrine Cache][]
- Optimize performance
- Fix issues with adhering to [PSR-6], [PSR-16] and [Doctrine Cache][]

Please see [CONTRIBUTING] for additional details.

## 🧪 Testing

```bash
$ composer test
```

This will tests biurad/php-cache will run against PHP 7.2 version or higher.

## 👥 Credits & Acknowledgements

- [Divine Niiquaye Ibok][@divineniiquaye]
- [Doctrine Team][]
- [All Contributors][]

This code is based on the [Doctrine Cache][] which is written, maintained and copyrighted by [Doctrine Team][]. This project simply wouldn't exist without their work.

## 🙌 Sponsors

Are you interested in sponsoring development of this project? Reach out and support us on [Patreon](https://www.patreon.com/biurad) or see <https://biurad.com/sponsor> for a list of ways to contribute.

## 📄 License

**biurad/php-cache** is licensed under the BSD-3 license. See the [`LICENSE`](LICENSE) file for more details.

## 🏛️ Governance

This project is primarily maintained by [Divine Niiquaye Ibok][@divineniiquaye]. Members of the [Biurad Lap][] Leadership Team may occasionally assist with some of these duties.

## 🗺️ Who Uses It?

You're free to use this package, but if it makes it to your production environment we highly appreciate you sending us an [email] or [message] mentioning this library. We publish all received request's at <https://patreons.biurad.com>.

Check out the other cool things people are doing with `biurad/php-cache`: <https://packagist.org/packages/biurad/cache/dependents>

[Composer]: https://getcomposer.org
[PSR-6]: http://www.php-fig.org/psr/psr-6/
[PSR-16]: http://www.php-fig.org/psr/psr-16/
[PHP]: https://php.net
[@divineniiquaye]: https://github.com/divineniiquaye
[docs]: https://docs.biurad.com/php-cache
[commit]: https://commits.biurad.com/php-cache.git
[UPGRADE]: UPGRADE-1.x.md
[CHANGELOG]: CHANGELOG-0.x.md
[CONTRIBUTING]: ./.github/CONTRIBUTING.md
[All Contributors]: https://github.com/biurad/php-cache/contributors
[Biurad Lap]: https://team.biurad.com
[email]: support@biurad.com
[message]: https://projects.biurad.com/message
[Doctrine Cache]: https://github.com/doctrine/cache
