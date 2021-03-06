# Ely.by PHP-CS-Fixer rules

Set of PHP-CS-Fixer rules used in development of Ely.by PHP projects. It's suited for PHP 7.1 and above.
You can use it as a ready-made set of rules or [just some of them](#using-our-fixers).

[![Latest Version on Packagist][ico-version]][link-packagist]
[![Total Downloads][ico-downloads]][link-downloads]
[![Software License][ico-license]](LICENSE.md)
[![Build Status][ico-build-status]][link-build-status]

## Installation

First of all install Ely.by PHP-CS-Fixer rules via composer with
[PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer):

```sh
composer require --dev friendsofphp/php-cs-fixer ely/php-code-style
```

Then create file `.php_cs` with following contents:

```php
<?php
$finder = \PhpCsFixer\Finder::create()
    ->in(__DIR__);

return \Ely\CS\Config::create()
    ->setFinder($finder);
```

And that's it. You can now find code style violations with following command:

```sh
vendor/bin/php-cs-fixer --diff --dry-run -v fix 
```

And then completely fix them all with:

```sh
vendor/bin/php-cs-fixer fix
```

### Configuration

You can pass a custom set of rules to the `\Ely\CS\Config::create()` call. For example, it can be used to validate a
project with PHP 7.0 compatibility:

```php
<?php
return \Ely\CS\Config::create([
    'visibility_required' => ['property', 'method'],
])->setFinder($finder);
```

## Code style

Our code style is based primarily on [PSR-2](https://www.php-fig.org/psr/psr-2/), while borrowing some ideas from
[PSR-12](https://github.com/php-fig/fig-standards/blob/92b198bb/proposed/extended-coding-style-guide.md)
with some changes.

### Example

This example encompasses some of the rules below as a quick overview:

```php
<?php
declare(strict_types=1);

namespace Vendor\Package;

use Vendor\Package\SomeNamespace\ClassA;

class Foo extends Bar implements FooInterface {
    use SomeTrait;

    private const SAMPLE_1 = 123;
    private const SAMPLE_2 = 321;

    public $field1;

    public $field2;

    public function sampleFunction(int $a, int $b = null): array {
        if ($a === $b) {
            $result = bar();
        } else {
            $result = BazClass::bar($this->field1, $this->field2);
        }

        return $result;
    }
    
    public function setToNull(): self {
        $this->field1 = null;
        return $this;
    }

}
```

**Key differences:**

* Opening braces for classes MUST be **on the same line**.

* Opening braces for methods MUST be **on the next line**.

**Additional rules:**

* There MUST be one empty line before `return` statement, except when there is only one statement before it.
  
  ```php
  <?php
  
  function a() {
      $a = '123';
      return $a . ' is a number';
  }

  function b() {
      $a = '123';
      $b = 'is';

      return $a . ' ' . $b . ' a number';
  }
  ```

* There MUST be one blank line around class body, but there MUST be **no blank lines** around anonymous class body.
  
  ```php
  <?php
  class Test {
  
      public function method() {
          $obj = new class extends Foo {
              public function overriddenMethod() {
                  // code body
              }
          };
      }
  
  }
  ```

* Visibility MUST be declared for all methods, properties and constants.

* There MUST be one blank line after an each of `if`, `switch`, `for`, `foreach`, `while` and `do-while` bodies.
  
  ```php
  <?php
  if (true) {
      // some actions here
  }
  
  echo 'the next statement is here';
  ```

## Using our fixers

First of all, you must install Ely.by PHP-CS-Fixer package as described in the [installation chapter](#installation).
After that you can enable our custom fixers with `registerCustomFixers` method:

```php
<?php
// Your Finder configuration

return \PhpCsFixer\Config::create()
    ->registerCustomFixers(new \Ely\CS\Fixers());
```

And then you'll be able to use our custom rules.

### `Ely/blank_line_around_class_body`

Ensure that a class body contains one blank line after its definition and before its end:

```diff
--- Original
+++ New
@@ @@
  <?php
  class Test {
+
      public function func() {
          $obj = new class extends Foo {
+
              public $prop;
+
          }
      }
+
  }
```

**Configuration:**

* `apply_to_anonymous_classes` - should this fixer be applied to anonymous classes? If it is set to `false`, than
  anonymous classes will be fixed to don't have empty lines around body. **Default**: `true`.

* `blank_lines_count` - adjusts an amount of the blank lines. **Default**: `1`.

### `Ely/blank_line_before_return`

This is extended version of the original `blank_line_before_statement` fixer. It applies only to `return` statements
and only in cases, when on the current nesting level more than one statements.

```diff
--- Original
+++ New
@@ @@
 <?php
 public function foo() {
     $a = 'this';
     $b = 'is';
+
     return "$a $b awesome";
 }

 public function bar() {
     $this->foo();
     return 'okay';
 }
```

### `Ely/line_break_after_statements`

Ensures that there is one blank line above the next statements: `if`, `switch`, `for`, `foreach`, `while`
and `do-while`.

```diff
--- Original
+++ New
@@ @@
 <?php
 $a = 123;
 if ($a === 123) {
     // Do something here
 }
+
 $b = [1, 2, 3];
 foreach ($b as $number) {
     if ($number === 3) {
         echo 'it is three!';
     }
 }
+
 $c = 'next statement';
```

### `Ely/new_with_braces`

This is the extended version of the original `new_with_braces` fixer. It allows you to remove braces around
an anonymous class declaration in a case when said class constructor doesn't contain any arguments.

```diff
--- Original
+++ New
@@ @@
 <?php
-$a = new Foo;
+$a = new Foo();
-$b = new class() extends Foo {};
+$b = new class extends Foo {};
```

**Configuration:**

* `remove_for_anonymous_classes` - if its set to `true`, then braces for anonymous classes without constructor
  arguments will be removed. **Default**: `false`.

### `Ely/remove_class_name_method_usages` (Yii2)

Replaces Yii2 [`BaseObject::className()`](https://github.com/yiisoft/yii2/blob/e53fc0ded1/framework/base/BaseObject.php#L84)
usages with native `::class` keyword, introduced in PHP 5.5.

```diff
--- Original
+++ New
@@ @@
  <?php
  use common\models\User;
  
- $className = User::className();
+ $className = User::class;
```

[ico-version]: https://img.shields.io/packagist/v/ely/php-code-style.svg?style=flat-square
[ico-license]: https://img.shields.io/badge/license-Apache-green.svg?style=flat-square
[ico-downloads]: https://img.shields.io/packagist/dt/ely/php-code-style.svg?style=flat-square
[ico-build-status]: https://img.shields.io/travis/elyby/php-code-style/master.svg?style=flat-square

[link-packagist]: https://packagist.org/packages/ely/php-code-style
[link-contributors]: ../../contributors
[link-downloads]: https://packagist.org/packages/ely/php-code-style/stats
[link-build-status]: https://travis-ci.org/elyby/php-code-style
