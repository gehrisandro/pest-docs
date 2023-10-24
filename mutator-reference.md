---
title: Mutator Reference
description: TODO
---

# Mutator Reference

A comprehensive list of all mutators available in [Pest Mutation Testing](/docs/mutation-testing).

TODO: This is only a small part of the mutators available. We need to update this documentation automatically from the plugin source code.

## Sets

The mutators are group in sets.

### Default

A set of mutators that are enabled by default. Which keep a good balance between performance and mutation coverage.

TODO: Explain in detail which mutators are included.

### Arithmetic

<div class="collection-method-list" markdown="1">

- [MinusToPlus](#minus-to-plus)
- [PlusToMinus](#plus-to-minus)

</div>

### Conditionals

<div class="collection-method-list" markdown="1">

- [IfAlwaysFalse](#if-always-false)
- [IfAlwaysTrue](#if-always-true)

</div>

---

<a name="minus-to-plus"></a>
### MinusToPlus

Converts all arithmetic minus operations to plus operations.

```php
$c = $a - $b;  // [tl! remove]
$c = $a + $b;  // [tl! add]
```

<a name="plus-to-minus"></a>
### PlusToMinus

Converts all arithmetic plus operations to minus operations.

```php
$c = $a + $b;  // [tl! remove]
$c = $a - $b;  // [tl! add]
```

<a name="if-always-false"></a>
### IfAlwaysFalse

Sets all `if()` conditions to false.

```php
if ($a > $b) {  // [tl! remove]
if (false) {  // [tl! add]
    return true;
}
```

<a name="if-always-true"></a>
### IfAlwaysTrue

Sets all `if()` conditions to true.

```php
if ($a > $b) {  // [tl! remove]
if (true) {  // [tl! add]
    return true;
}
```

---

In this chapter, you found a complete list of CLI options provided by Pest. In the subsequent documentation, we will explore the topic of test dependencies: [Test Dependencies](/docs/test-dependencies)
