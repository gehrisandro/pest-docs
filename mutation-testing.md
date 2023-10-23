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

<a name="configuration"></a>
### Configuration

You can configure mutation testing in you Pest.php file.

```php
mutate()
    ->paths('src');
```

<a name="profiles"></a>
#### Alternative configuration profiles

You can create more mutation testing profiles.

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

#### Options

##### `paths()` (CLI: `--paths`)

Limit the directories or files to mutate by providing one or more paths to a directory or file to test.

You can also use patterns.

```php
mutate()
    ->paths('src');
```

##### `group()` (CLI: `--group`)

Limit the tests to run by providing one or more group names.

```php
mutate()
    ->group('unit');
```

##### `mutators()` (CLI: `--mutators`)

Choose the mutators you want to use. Choose from various sets or provide individual mutators. If not set, `Mutators::SET_DEFAULT` is used. 

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

### Run mutation testing

#### CLI

You can run the mutation testing from the CLI providing the `--mutate` option.

```bash
vendor/bin/pest --mutate
```

By default it inherits the default profile (if available) or you can inherit a different [profile](#profiles) by providing its name.

```bash
vendor/bin/pest --mutate="arithmetic only"
```

##### Options

You can set or overwrite all options available in the [configuration](#configuration) via CLI options.

```bash
vendor/bin/pest --mutate --paths=src
```

Additionally, you can make use of most of the options available in Pest. \
For example this would only run mutation tests for code covered by test from the "unit" group.

```bash
vendor/bin/pest --mutate --group=unit
```

#### CI/CD

TODO

#### `mutate()`

You can use the `mutate()` function on test cases or describe blocks to run the mutations only for code covered by this test or describe block.

By default, it inherits its configuration from the default profile. You can change this by providing a profile name.

```php
test('sum', function () {
  $result = sum(1, 2);

  expect($result)->toBe(3);
})->mutate();

// or ->mutate('arithmetic only') - to inherit from the 'arithmetic only' profile
```

Executing the `./vendor/bin/pest` command will only run mutation tests for code covered by the test. It is not necessary to provide the `--mutate` option.

If necessary you can override the configuration of the profile.

```php
test('sum', function () {
  $result = sum(1, 2);

  expect($result)->toBe(3);
})->mutate()
  ->paths('src');
```

### Performance

Mutation testing is potentially very time consuming and resource intensive because of the sheer amount of possible mutations and tests to run them against.

Therefore, Pest Muation Testing is optimized to limit the amount of mutations and tests to run against as much as possible. To achieve this, it uses the following strategies:
- Limit the amount of possible mutations by having a carefully chosen set of mutators
- Run only tests the covers the mutated code (only if code coverage driver is installed or a coverage report is available)
- It tries to use cached mutations and test runs from previous executions
- Run tests in a decent order (fastet test first)

But there is much more you can do to improve performance. Especially if you have a larger code base and/or you are using mutations testing while developing locally.

#### Use a code coverage driver

If you have a code coverage driver available, Pest will use it to only run tests that cover the mutated code.

#### Reduce the number of mutators

Reduce the number of mutations by choosing a smaller set of mutators.

```php
vendor/bin/pest --mutate --mutators=ArithmeticPlusToMinus
```

#### Reduce the number of files to mutate

Reduce the number of mutations by only mutating a subset of your code base.

```bash
vendor/bin/pest --mutate --paths=src/path/file.php
```

<a name="only-run-parts-of-your-test-suite"></a>
#### Only run parts of your test suite

Reduce the number of mutations and tests to execute by only running a subset of your test suite.

```bash
vendor/bin/pest --mutate --filter=SumTest
```

For more filter options see [Filtering](/docs/filtering-tests).

#### Only create mutations for covered files / lines

Reduce the number of mutations by only mutating code that is covered by tests. This is especially helpful if you are running only a subset of your test suite. See [Only run parts of your test suite](#only-run-parts-of-your-test-suite).

```bash
vendor/bin/pest --mutate --covered-only
```

> Attention: Code not covered by tests will not be mutated. 

#### Run tests in parallel

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

---

In this chapter, we've seen XYZ. In the following chapter, we will dive into XYZ: [XYZ](/docs/xyz)
