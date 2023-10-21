---
title: Mutation Testing
description: Mutation testing is a way... TODO 
---

# Mutation Testing

<a name="mutate"></a>
### `mutate()`

If you want to mutate only code covered by this test.

```bash
test('sum', function () {
  $result = sum(1, 2);

  expect($result)->toBe(3);
})->mutate();
```

#### `mutate()` options




---

TODO
In this chapter, we've seen how powerful snapshot testing is. In the following chapter, we will dive into Pest's custom helpers: [Custom Helpers](/docs/custom-helpers)
