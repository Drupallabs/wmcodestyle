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
    * [PHP-CS-Fixer](#php-cs-fixerhttpgithubcomfriendsofphpphp-cs-fixer)
    * [Rector](#rectorhttpsgithubcomrectorphprector)
    * [PHPStan](#phpstanhttpsgithubcomphpstanphpstan)
    * [composer-normalize](#composer-normalizehttpsgithubcomergebniscomposer-normalize)
    * [Running code style fixers](#running-code-style-fixers)
3. [Changelog](#changelog)
4. [Security](#security)
5. [License](#license)
6. [Acknowledgments](#acknowledgments)

## Coding standards
- [General](standards/GENERAL.md)
- [Code structure](standards/CODE_STRUCTURE.md)
- [Spaces and indentation](standards/SPACES_INDENTATION.md)
- [Comments and docblocks](standards/COMMENTS_DOCBLOCKS.md)
- [Models](standards/MODELS.md)
- [Controllers](standards/CONTROLLERS.md)
- [Event subscribers](standards/EVENT_SUBSCRIBERS.md)
- [Drush commands](standards/DRUSH_COMMANDS.md)
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
     "php": "^8.0",
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

### [PHP-CS-Fixer](http://github.com/FriendsOfPHP/PHP-CS-Fixer)

This package provides a configuration factory and multiple rule sets for
[`friendsofphp/php-cs-fixer`](http://github.com/FriendsOfPHP/PHP-CS-Fixer).

#### Configuration

Pick one of the rule sets:

* [`Wieni\wmcodestyle\PhpCsFixer\Config\RuleSet\Php71`](src/PhpCsFixer/Config/RuleSet/Php71.php)
* [`Wieni\wmcodestyle\PhpCsFixer\Config\RuleSet\Php73`](src/PhpCsFixer/Config/RuleSet/Php73.php)
* [`Wieni\wmcodestyle\PhpCsFixer\Config\RuleSet\Php74`](src/PhpCsFixer/Config/RuleSet/Php74.php)
* [`Wieni\wmcodestyle\PhpCsFixer\Config\RuleSet\Php80`](src/PhpCsFixer/Config/RuleSet/Php80.php)

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

$config->setCacheFile(__DIR__ . '/.cache/php_cs.cache');

return $config;
```

By default, risky rules are not used. To use them, pass `--allow-risky=yes` to `php-cs-fixer` or set the 
`WMCODESTYLE_RISKY=1` environment variable.

#### Git

All configuration examples use the caching feature, and if you want to
use it as well, you should add the cache folder to `.gitignore`:

```gitignore
# Ignore files generated by wieni/wmcodestyle
.cache
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

### [Rector](https://github.com/rectorphp/rector)
> Instant upgrade and refactoring of your PHP code

This package provides multiple rule sets for [rector/rector](https://github.com/rectorphp/rector). These rule sets are largely identical to the default rule sets, with the exception of a couple individual rules we don't like/need. Instead of [rector/rector](https://github.com/rectorphp/rector), [rector/rector-prefixed](https://github.com/rectorphp/rector-prefixed) is added as dependency to make it easier to include this package in your Composer project.

#### Rule sets

Pick one of the rule sets:

* [`Wieni\wmcodestyle\Rector\SetList\WieniSetList\CODE_QUALITY`](rector/code-quality.php)
* [`Wieni\wmcodestyle\Rector\SetList\WieniSetList\CODING_STYLE`](rector/coding-style.php)
* [`Wieni\wmcodestyle\Rector\SetList\WieniSetList\DEPENDENCY_INJECTION`](rector/dependency-injection.php)
* [`Wieni\wmcodestyle\Rector\SetList\WieniSetList\EARLY_RETURN`](rector/early-return.php)
* [`Wieni\wmcodestyle\Rector\SetList\WieniSetList\TYPE_DECLARATION`](rector/type-declaration.php)

#### Example configuration

```php
<?php

declare(strict_types=1);

use Rector\Core\Configuration\Option;
use Rector\Set\ValueObject\SetList;
use Rector\Symfony\Set\TwigSetList;
use Symfony\Component\DependencyInjection\Loader\Configurator\ContainerConfigurator;
use Wieni\wmcodestyle\Rector\SetList as WieniSetList;

return static function (ContainerConfigurator $containerConfigurator): void {
    $parameters = $containerConfigurator->parameters();

    $parameters->set(Option::AUTO_IMPORT_NAMES, true);
    $parameters->set(Option::IMPORT_SHORT_CLASSES, false);

    $parameters->set(Option::PATHS, [
        __DIR__ . '/public/modules/custom',
    ]);

    $parameters->set(Option::AUTOLOAD_PATHS, [
        __DIR__ . '/public/core',
        __DIR__ . '/public/core/modules',
        __DIR__ . '/public/modules',
        __DIR__ . '/public/profiles',
        __DIR__ . '/public/sites',
        __DIR__ . '/public/themes',
    ]);

    $parameters->set(Option::SETS, [
        SetList::DEAD_CODE,
        SetList::PHP_73,
        SetList::PHP_74,
        TwigSetList::TWIG_UNDERSCORE_TO_NAMESPACE,
    ]);

    $containerConfigurator->import(WieniSetList::CODE_QUALITY);
    $containerConfigurator->import(WieniSetList::CODING_STYLE);
    $containerConfigurator->import(WieniSetList::DEPENDENCY_INJECTION);
    $containerConfigurator->import(WieniSetList::EARLY_RETURN);
    $containerConfigurator->import(WieniSetList::TYPE_DECLARATION);
};
```

#### Add Symfony container XML
To work with some Symfony rules, you now need to link your container XML file:

```php
<?php

declare(strict_types=1);

use Rector\Core\Configuration\Option;
use Rector\Set\ValueObject\SetList;
use Symfony\Component\DependencyInjection\Loader\Configurator\ContainerConfigurator;
use Wieni\wmcodestyle\Rector\SetList as WieniSetList;

return static function (ContainerConfigurator $containerConfigurator): void {
    $parameters = $containerConfigurator->parameters();
    $parameters->set(Option::SYMFONY_CONTAINER_XML_PATH_PARAMETER, __DIR__ . '/.cache/drupal_container.xml');
};
```

```neon
// phpstan-for-rector.neon
parameters:
    symfony:
        container_xml_path: %currentWorkingDirectory%/.cache/drupal_container.xml

includes:
	- vendor/wieni/wmcodestyle/phpstan/for-rector.neon
```

Dumping the container is not possible out-of-the-box when using Drupal, but our [Container Dumper module](https://github.com/wieni/container_dumper) makes this possible.

### [PHPStan](https://github.com/phpstan/phpstan)
> PHPStan focuses on finding errors in your code without actually running it. It catches whole classes of bugs even 
> before you write tests for the code. It moves PHP closer to compiled languages in the sense that the correctness of 
> each line of the code can be checked before you run the actual line.

_TODO_

### [composer-normalize](https://github.com/ergebnis/composer-normalize)
> Provides a composer plugin for normalizing composer.json.

We highly recommend this Composer plugin to make sure your composer.json is formatted consistently.

### Running code style fixers

#### Makefile

If you like [`Makefile`](https://www.gnu.org/software/make/manual/make.html#Introduction)s, create a `Makefile` with a 
`coding-standards` target:

```diff
+.PHONY: coding-standards
+coding-standards: vendor
+    mkdir -p .build/php-cs-fixer
+    vendor/bin/php-cs-fixer fix --config=.php_cs --diff --verbose
+    composer normalize"
+    vendor/bin/rector process"
+    vendor/bin/php-cs-fixer fix --config=.php_cs.php
+    vendor/bin/phpstan analyse

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
     "php": "^8.0",
   },
   "require-dev": {
     "wieni/wmcodestyle": "^1.0"
+  },
+  "scripts": {
+    "coding-standards": [
+      "@composer normalize",
+      "rector process",
+      "php-cs-fixer fix --config=.php_cs.php",
+      "phpstan analyse"
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
