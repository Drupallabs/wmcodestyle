wmcodestyle
======================

[![Latest Stable Version](https://poser.pugx.org/wieni/wmcodestyle/v/stable)](https://packagist.org/packages/wieni/wmcodestyle)
[![Total Downloads](https://poser.pugx.org/wieni/wmcodestyle/downloads)](https://packagist.org/packages/wieni/wmcodestyle)
[![License](https://poser.pugx.org/wieni/wmcodestyle/license)](https://packagist.org/packages/wieni/wmcodestyle)

> A set of Wieni best practices, coding standards and tools to enforce them.

## Why?
- A central location to keep track of our written coding standards and 
 best practices
- Make configuration for tools to enforce our codestyle available in all
  of our repositories, without having to duplicate and sync them
  manually  
 
## Table of Contents
1. [Coding standards](#coding-standards-and-best-practices)
2. [Tooling](#tooling)
    * [Installation](#installation)
    * [Sync config files with your project](#sync-config-files-with-your-project)
    * [Configuration for php-cs-fixer](#configuration-for-php-cs-fixer)
3. [Changelog](#changelog)
4. [Security](#security)
5. [License](#license)
6. [Acknowledgments](#acknowledgments)

## Coding standards
- [Publishing modules](standards/PUBLISHING_MODULES.md)
- [Git](standards/GIT.md)

## Tooling
### Installation

This package requires PHP 7.1 or higher and can be installed using
Composer:

```bash
 composer require --dev wieni/wmcodestyle
```

### Sync config files with your project
This package provides a command to sync any file from this repository to
your project. It is recommended to add it to the `scripts` section of
composer.json so it is automatically executed at the appropriate time. 

```diff
 {
   "name": "foo/bar",
   "require": {
     "php": "^7.2",
   },
   "require-dev": {
     "wieni/wmcodestyle": "^1.0"
+  },
+  "scripts": {
+    "post-update-cmd": [
+      "wmcodestyle sync .editorconfig --quiet"
+    ]
   }
 }
```

### Configuration for php-cs-fixer

This package provides a configuration factory and multiple rule sets for
[`friendsofphp/php-cs-fixer`](http://github.com/FriendsOfPHP/PHP-CS-Fixer).

#### Configuration

Pick one of the rule sets:

* [`Wieni\wmcodestyle\PhpCsFixer\Config\RuleSet\Php71`](src/PhpCsFixer/Config/RuleSet/Php71.php)
* [`Wieni\wmcodestyle\PhpCsFixer\Config\RuleSet\Php73`](src/PhpCsFixer/Config/RuleSet/Php73.php)

Create a configuration file `.php_cs.php` in the root of your project:

```php
<?php

use Wieni\wmcodestyle\PhpCsFixer\Config\Factory;
use Wieni\wmcodestyle\PhpCsFixer\Config\RuleSet\Php73;

$config = Factory::fromRuleSet(new Php73);

$config->getFinder()
    ->ignoreVCSIgnored(true)
    ->in(__DIR__)
    ->name('/\.(php|module|inc|install|test|profile|theme)$/')
    ->notPath('/(public|web)\/(index|update)\.php/');

$config->setCacheFile(__DIR__ . '/.php_cs.cache');

return $config;
```

#### Git

All configuration examples use the caching feature, and if you want to
use it as well, you should add the cache file to `.gitignore`:

```gitignore
# Ignore files generated by wieni/wmcodestyle
.php_cs.cache
```

#### Configuration with override rules

:bulb: Optionally override rules from a rule set by passing in an array of rules to be merged in:

```diff
<?php

use Wieni\wmcodestyle\PhpCsFixer\Config\Factory;
use Wieni\wmcodestyle\PhpCsFixer\Config\RuleSet\Php73;

-$config = Factory::fromRuleSet(new Php73);
+$config = Factory::fromRuleSet(new Php73, [
+    'mb_str_functions' => false,
+    'strict_comparison' => false,
+]);

$config->getFinder()
    ->ignoreVCSIgnored(true)
    ->in(__DIR__)
    ->name('/\.(php|module|inc|install|test|profile|theme)$/')
    ->notPath('/(public|web)\/(index|update)\.php/');

$config->setCacheFile(__DIR__ . '/.php_cs.cache');

return $config;
```

#### Makefile

If you like [`Makefile`](https://www.gnu.org/software/make/manual/make.html#Introduction)s, create a `Makefile` with a `coding-standards` target:

```diff
+.PHONY: coding-standards
+coding-standards: vendor
+	 mkdir -p .build/php-cs-fixer
+    vendor/bin/php-cs-fixer fix --config=.php_cs --diff --verbose

 vendor: composer.json composer.lock
     composer validate
     composer install
```

Run

```
$ make coding-standards
```

to automatically fix coding standard violations.

#### Composer script

If you like [`composer` scripts](https://getcomposer.org/doc/articles/scripts.md), add a `coding-standards` script to `composer.json`:

```diff
 {
   "name": "foo/bar",
   "require": {
     "php": "^7.2",
   },
   "require-dev": {
     "wieni/wmcodestyle": "^1.0"
+  },
+  "scripts": {
+    "coding-standards": [
+      "php-cs-fixer fix --config=.php_cs.php"
+    ]
   }
 }
```

Run

```
$ composer coding-standards
```

## Changelog
All notable changes to this project will be documented in the
[CHANGELOG](CHANGELOG.md) file.

## Security
If you discover any security-related issues, please email
[security@wieni.be](mailto:security@wieni.be) instead of using the issue
tracker.

## License
Distributed under the MIT License. See the [LICENSE](LICENSE.md) file
for more information.

## Acknowledgments
- [ergebnis/php-cs-fixer-config](https://github.com/ergebnis/php-cs-fixer-config)
