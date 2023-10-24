---
title: Mutation Testing
description: Mutation testing is a way... TODO 
---

# Mutation Testing

> Note: Before you can start using mutation testing, you need to have a successfully running test suite.

**Source code**: [github.com/pestphp/pest-plugin-xyz](XYZ)

TODO: Motivitation / Why use mutation testing?

To start using Pest's Mutation plugin, you need to require the plugin via Composer.

```bash
composer require pestphp/pest-plugin-xyz --dev
```

After requiring the plugin, you may utilize the `--mutate` option to run mutation testing.

```bash
./vendor/bin/pest --mutate
```

For more ways to run visit [Run mutation testing](#run-mutation-testing).

<a name="configuration"></a>
## Configuration

You can configure mutation testing in you Pest.php file.

```php
mutate()
    ->paths('src');
```

For all the available options see [Options](#options) section.

<a name="profiles"></a>
### Alternative configuration profiles

You can create multiple mutation testing profiles.

```php
use XYZ;

mutate('arithmetic only') // 'default' if not provided
    ->paths('src')
    ->mutators(Mutators::SET_ARITHMETIC);
```

And you can inherit from another profile.

```php
use XYZ;

mutate('arithmetic only')
    ->extends('default')
    ->mutators(Mutators::SET_ARITHMETIC);
```

<a name="run-mutation-testing"></a>
## Run mutation testing

### Run in CLI

You can run the mutation testing from the CLI providing the `--mutate` option.

```bash
vendor/bin/pest --mutate
```

By default, it uses the default profile (if available) or you can use a different [profile](#profiles) by providing its name.

```bash
vendor/bin/pest --mutate="arithmetic only"
```

You can set or overwrite all [options](#options) available.

```bash
vendor/bin/pest --mutate --paths=src
```

Additionally, you can make use of most of the options available in Pest. \
For example this would only run mutation tests for code covered by test from the "unit" group.

```bash
vendor/bin/pest --mutate --group=unit
```

### `mutate()`

You can use the `mutate()` function on test cases or describe blocks to run the mutations only for code covered by the given test or describe block.

This function is intended to be used in your daily development workflow to establish a mutation testing practice right when you are writing your tests. 

By default, it inherits its configuration from the default profile. You can change this by providing a profile name.

```php
test('sum', function () {
  $result = sum(1, 2);

  expect($result)->toBe(3);
})->mutate(); // or ->mutate('arithmetic only')
```

Executing the `./vendor/bin/pest` command will automatically run mutation testing. It is not necessary to provide the `--mutate` option.

If necessary you can override the configuration of the profile.

```php
test('sum', function () {
  $result = sum(1, 2);

  expect($result)->toBe(3);
})->mutate()
  ->paths('src');
```

> Using the `mutate()` function on a test case or describe block will only create mutations for code covered by the given test or describe block.
> To disable this behaviour append `->coveredOnly(false)`.

### CI/CD

TODO: How to run mutation testing in CI/CD

<a name="options"></a>
## Options

With Pest Mutation testing, you can configure the following options.

<div class="collection-method-list" markdown="1">

- [`paths()`](#options-paths)
- [`mutators()`](#options-mutators)
- [`except()`](#options-except)
- [`coveredOnly()`](#options-covered-only)
- [`uncommittedOnly()`](#options-uncommitted-only)
- [`changedOnly()`](#options-changed-only)
- [`stopOnSurvival()`](#options-stop-on-survival)
- [`stopOnUncovered()`](#options-stop-on-uncovered)

</div>

---


<a name="options-paths"></a>
### `paths()`
CLI: `--paths`

Limit the directories or files to mutate by providing one or more paths to a directory or file to test.

You can also use patterns.

```php
mutate()
    ->paths('src');
```


<a name="options-mutators"></a>
### `mutators()`
CLI: `--mutators`

Choose the mutators you want to use. Choose from various sets or provide individual mutators. If not set, `Mutators::SET_DEFAULT` is used.

A list of all available mutators can be found in the [Mutator Reference](/docs/mutator-reference).

```php
use XYZ;

mutate()
    ->mutators(Mutators::SET_ARITHMETIC);
// or
mutate()
    ->mutators(Mutators::ARITHMETIC_PLUS_TO_MINUS, Mutators::ARITHMETIC_MINUS_TO_PLUS);
```

On the CLI you can provide a comma separated list of mutator names.

```bash
vendor/bin/pest --mutate --mutators=ArithmeticPlusToMinus,ArithmeticMinusToPlus
```


<a name="options-except"></a>
### `except()`
CLI: `--except`

Exclude specific mutators from being used. Especially useful if you want to use a set of mutators but want to exclude some of them.

```php
use XYZ;

mutate()
    ->mutators(Mutators::SET_ARITHMETIC);
    ->exclude(Mutators::ARITHMETIC_PLUS_TO_MINUS);
```


<a name="options-covered-only"></a>
### `coveredOnly()`
CLI: `--covered-only`

Limit mutations to code that is covered by tests. This is especially helpful if you are running only a subset of your test suite. See [Only run parts of your test suite](#only-run-parts-of-your-test-suite).

```php
mutate()
    ->coveredOnly();
```


<a name="options-uncommitted-only"></a>
### `uncommittedOnly()`
CLI: `--uncommitted-only`

Limit mutations to code that has uncommitted changes.

```php
mutate()
    ->uncommittedOnly();
```


<a name="options-changed-only"></a>
### `changedOnly()`
CLI: `--changed-only`

Limit mutations to code that has changed relative to a common ancestor of the given branch (defaults to `main`).

```php
mutate()
    ->changedOnly(); // or ->changedOnly('master');
```


<a name="options-stop-on-survival"></a>
### `stopOnSurvival()`
CLI: `--stop-on-survival`

Stop execution upon first survived mutant.

```php
mutate()
    ->stopOnSurvival();
```


<a name="options-stop-on-uncovered"></a>
### `stopOnUncovered()`
CLI: `--stop-on-uncovered`

Stop execution upon first uncovered mutant.

```php
mutate()
    ->stopOnUncovered();
```

## Performance

Mutation testing is potentially very time consuming and resource intensive because of the sheer amount of possible mutations and tests to run them against.

Therefore, Pest Muation Testing is optimized to limit the amount of mutations and tests to run against as much as possible. To achieve this, it uses the following strategies:
- Limit the amount of possible mutations by having a carefully chosen set of mutators
- Run only tests that covers the mutated code (only if code coverage driver is installed or a coverage report is available)
- It tries to use cached mutations and test runs from previous executions
- Run tests in a decent order (fastet test first)

But there is much more you can do to improve performance. Especially if you have a larger code base and/or you are using mutations testing while developing locally.

### Use a code coverage driver

If you have a code coverage driver available, Pest will use it to only run tests that cover the mutated code.

**Supports [XDebug 3.0+](https://xdebug.org/docs/install/)** or [PCOV](https://github.com/krakjoe/pcov).

### Reduce the number of mutators

Reduce the number of mutations by choosing a smaller set of mutators.

```php
vendor/bin/pest --mutate --mutators=ArithmeticPlusToMinus
```

### Reduce the number of files to mutate

Reduce the number of mutations by only mutating a subset of your code base.

```bash
vendor/bin/pest --mutate --paths=src/path/file.php
```

<a name="only-run-parts-of-your-test-suite"></a>
### Only run parts of your test suite

Reduce the number of mutations and tests to execute by only running a subset of your test suite.

```bash
vendor/bin/pest --mutate --filter=SumTest
```

For more filter options see [Filtering](/docs/filtering-tests).

### Only create mutations for covered files / lines

Reduce the number of mutations by only mutating code that is covered by tests. This is especially helpful if you are running only a subset of your test suite. See [Only run parts of your test suite](#only-run-parts-of-your-test-suite).

```bash
vendor/bin/pest --mutate --covered-only
```

> Attention: Code not covered by tests will not be mutated. 

### Run tests in parallel

```bash
vendor/bin/pest --mutate --parallel
```

## Ignoring Mutations

Sometimes, you may want to ignore a specific mutation or line of code. To do so, you may use the `@pest-ignore-mutation` annotation:

```php
    if($age >= 18) // @pest-ignore-mutation
        // ...
    ];
}
```

## Minimum Threshold Enforcement

Just like code coverage, mutation coverage can also be enforced. You can use the `--mutate` and `--min` options to define the minimum threshold values for the mutation score index. If the specified thresholds are not met, Pest will report a failure.

```bash
./vendor/bin/pest --mutate --min=100
```

## Custom Mutators

You may want to create your own custom mutators. You can do so by creating a class that implements the `Mutator` interface. \
This example will remove `use` statements.

```php
namespace App\Mutators;

use Pest\XYZ\Contracts\Mutator;
use PhpParser\Node;
use PhpParser\NodeTraverser;

class RemoveUseStatement implements Mutator
{
    public static function can(Node $node): bool
    {
         return $node instanceof Node\Stmt\Use_;
    }

    public static function mutate(Node $node): int
    {
        return NodeTraverser::REMOVE_NODE;
    }
}
```

Afterward you can use your mutator.

```php
use App\Mutators\RemoveUseStatement;

mutate()
    ->mutators(RemoveUseStatement::class);
```

In the CLI you must provide the full class name.

```bash
vendor/bin/pest --mutate --mutators="App\\Mutators\\RemoveUseStatement"
```


---

In this chapter, we've seen XYZ. In the following chapter, we will dive into XYZ: [XYZ](/docs/xyz)
