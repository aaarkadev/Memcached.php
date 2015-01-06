Memcached.php
=============

Plain vanilla PHP `Memcached` client library with nearly full support of the `Memcached` ASCII-protocol specification.

[![Build Status](https://travis-ci.org/clickalicious/Memcached.php.svg?branch=master)](https://travis-ci.org/clickalicious/Memcached.php)
<a href="https://twitter.com/intent/tweet?hashtags=&original_referer=http%3A%2F%2Fgithub.com%2F&text=Memcached.php%20-%20Plain%20vanilla%20PHP%20Memcached%20client%20library%20https%3A%2F%2Fgithub.com%2Fclickalicious%2FMemcached.php&tw_p=tweetbutton" target="_blank">
  <img src="http://jpillora.com/github-twitter-button/img/tweet.png"></img>
</a>
[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/clickalicious/memcached.php/trend.png)](https://bitdeli.com/free "Bitdeli Badge")


## Features

 - Nearly 100% of `Memcached` ASCII-protocol specification covered
 - Clean & well documented code 
 - Unit-Tested
 - Support for storing native PHP variable types (arrays, objects ...)
 - Increment & Decrement support
 - Efficient connection sharing  
 - Configurable connection close behavior

**Memcached.php** covers almost 100% of the `Memcached` protocol specification. The code is clean, full documented and developed following the PSR coding standards (PSR-0/4, PSR-1, PSR-2). The code is unit-tested (PHPUnit) and the coverage is above 90%. The library supports \<incr\> and \<decr\> command on stored integers (strings) and the [connection handling is done like recommended](https://github.com/memcached/memcached/blob/master/doc/protocol.txt#L10 "Keep connections open and share them via a pool across instances.") in the `Memcached` protocol specification. Last but not least it supports seven of [PHP's eight variable types](http://php.net/manual/en/language.types.intro.php "PHP's variable types") - in detail four scalar types:

    boolean
    integer
    float (floating-point number [double])
    string

two compound types:

    array
    object

and finally one special type:

    NULL

So `resource` is the only type not supported.


## Requirements

 - PHP >= 5.5 (will be fixed soon to support PHP >= 5.3 ;)


## Philosophy

This client is neither tested nor designed to be used in heavy load environments. It was designed and developed by me as a client library for my [phpMemAdmin](https://github.com/clickalicious/phpMemAdmin "phpMemAdmin on github") project. So I was able to remove dependencies of both `Memcache` + `Memcached` (PECL) extensions - both are designed in a way i don't like. I've tried to align 100% with the Memcached protocol specification. In some cases I didn't liked the naming convention and so I created some proxies. As an example - I decided to implement increment() as proxy to incr() and decrement() as proxy to decr(). I will add some more responsibilities in some more classes like a [PSR compatible](https://github.com/php-fig/fig-standards/blob/master/proposed/cache.md "PSR Cache proposal") Caching proxy and a Pool/Cluster Class for management operations soon.


## Installation

The recommended way to install this library is through [Composer](http://getcomposer.org/). Require the `clickalicious/memcached.php` package into your `composer.json` file:

```json
{
    "require": {
        "clickalicious/memcached.php": "dev-master"
    }
}
```

**Memcached.php** is also available as [download from github packed as zip-file](https://github.com/clickalicious/Memcached.php/archive/master.zip "zip package containing library for download") or via `git clone https://github.com/clickalicious/Memcached.php.git .`.

## Usage

Simple demonstration to get started:
 - Create a `client` instance and connect it (*lazy*) to `Memcached` daemon on host *127.0.0.1* (on default port [11211])
 - Set *key* **foo** with *value* **1.00** 
 - Retrieve *value* for *key* **foo**

```php
$client = new \Clickalicious\Memcached\Client('127.0.0.1');

// Set a value of type float   
$client->set('foo', 1.00);

// Returns 1.00 as PHP's type float!     
$client->get('foo');   
``` 
You will find a demonstration (`Demo.php`) showing in detail how to use the **Memcached.php** `client`.


## Data

`String-`, `Integer-` and `Float/Double-types` are never modified by this library. Those types will be stored by `Memcached`'s internal system - while all other types will be serialized by this client and can optionally be stored compressed - in one of the next releases of this library. I'm working on an PoC implementation of `Smaz - a short string compression library` (https://github.com/zhenhao/smaz.php) and on a german translation of the translation table used by `Smaz`.


## Metadata

`Memcached` provides a 32 Bit (Version > 1.2.1) unsigned Integer field for meta data. From the `Memcached` protocol specification: 
> Note that in memcached 1.2.1 and higher, flags may be 32-bits, instead
of 16, but you might want to restrict yourself to 16 bits for
compatibility with older versions.

**Memcached.php** uses this field for its meta data. The meta data is required to mark data for serialization and stuff like this. This meta data is stored via the clients` flags field. The lower first **8 Bits** (*lowest Byte*) are reserved by **Memcached.php**. The other 8 Bits (half of the 16 Bits) can be used by your app.


## Documentation

The best and currently only existing documentation is the inline documentation of this project. So please have a look at the source to understand how **Memcached.php** works internally.


## Tests

**Memcached.php** is unit-tested and the coverage is above 90%. You will find a PHPUnit configuration including testsuites in directory `tests/`.

To run tests execute the following command on `cli`:

```sh
phpunit -c tests/phpunit.xml --testdox
```


### Latest Tests
Client
 - [x] Set a key value pair
 - [x] Get a value by key
 - [x] Add a key value pair
 - [x] Replace an existing value
 - [x] Append a value to an existing one
 - [x] Prepend a value to an existing one
 - [x] Cas set a key value pair
 - [x] Send a valid custom command string
 - [x] Send an invalid custom command string
 - [x] Retrieve version
 - [x] Storing php type string
 - [x] Storing php type float
 - [x] Storing php type integer
 - [x] Storing php type array
 - [x] Storing php type object
 - [x] Storing php type null
 - [x] Storing php type boolean
 - [x] Increment a stored value
 - [x] Decrement a stored value
 - [x] Connect to a memcached daemon
 - [x] Retrieve stats
 - [x] Trigger and handle error
 - [x] Trigger and handle client error
 - [x] Trigger and handle server error

The unit-tests are fired against an existing and real `Memcached` daemon. Please be aware that you need a running `Memcached` deamon on the host you run the unit-tests listening on the default port (11211). In Result the unit-tests are not that isolated cause they are bound to a running `Memcached` daemon and network as well.


## Versioning
For a consistent versioning i decided to make use of `Semantic Versioning 2.0.0` http://semver.org. Its easy to understand, very common and known by many other software products. Starting with release 0.1.0 (see releases) soon. 


## Roadmap

 - [ ] `\Clickalicious\Memcached\Proxy`  
   This should become a proxy implementation which is able to act as `Memcache` or `Memcached` (both PECL) extension (emulate) for testing (primary mocking/stubbing). 
 - [ ] `\Clickalicious\Memcached\Server`  
   This should become a virtual (emulated) mode which emulates a complete `Memcached` backend.
 - [ ] Refactoring the ugly parts in code.
 - [ ] Replace explodes and array operations for data anlysis with regular expressions.

If you are interested in any of these features too - please let me know. Maybe we can adjust the priority and speed things up ...


## Participate

... yeah. If you're a code monkey too - maybe we can build a force ;) If you would like to participate in either **Code**, **Comments**, **Documentation**, **Wiki**, **Bug-Reports**, **Unit-Tests**, **Bug-Fixes**, **Feedback** and/or **Critic** then please let me know as well!


## License
**BSD-3-Clause** 
See [BSD-3-Clause](http://opensource.org/licenses/BSD-3-Clause "BSD-3-Clause") or LICENSE file for details.


## Sponsors  
Thanks to our sponsors and supporters:  
<a href="https://www.jetbrains.com/phpstorm/" title="PHP IDE :: JetBrains PhpStorm" target="_blank">
    <img src="https://www.jetbrains.com/phpstorm/documentation/docs/logo_phpstorm.png"></img>
</a>
