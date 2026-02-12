---
title: "JSLT: removing duplicates by key"
date: "2025-02-12"
tags:
  - jslt
  - json
  - tips
---

When you end up with an array that has duplicate entries by some key (e.g. `id`, `tid`), you can dedupe in JSLT by turning the array into an **object keyed by that field**, then turning the object’s values back into an array. Duplicate keys overwrite, so you keep one entry per key.

---

### Pattern

1. **Object from array:** `{ for ($array) .keyField : . }`  
   Builds an object: key = `.keyField`, value = the whole element. Same key appears more than once → later value wins → unique keys.

2. **Array from object:** `[ for ($object) .value ]`  
   Iterating over an object gives key/value; take `.value` and you get the original elements back, now unique by that key.

**Together:**

```json
let withDuplicates = [
  { "id": "a", "name": "Alpha" },
  { "id": "b", "name": "Beta" },
  { "id": "a", "name": "Alpha again" }
]
let byId = { for ($withDuplicates) .id : . }
[ for ($byId) .value ]
```

**Result:** two objects (one per distinct `id`), not three. The second `"a"` overwrote the first in `byId`.

---

### In a function

Same idea in a reusable helper:

```json
def dedupe-by-key(array, keyField)
  let obj = { for ($array) .[$keyField] : . }
  [ for ($obj) .value ]
```

Use it like `dedupe-by-key($myArray, "tid")` (or whatever your key field is). If JSLT doesn’t support dynamic field access, inline the key:

```json
def dedupe-by-tid(array)
  let obj = { for ($array) .tid : . }
  if ($obj)
    [ for ($obj) .value ]
  else
    []
```

Then `dedupe-by-tid($sharedDevicesWithDuplicates)` gives you devices unique by `tid`.

---

### Where this fits

Typical flow:

1. Build an array that might have duplicates (e.g. from nested loops, flattened links, or merging two lists by a key).
2. Dedupe with the object trick: `let unique = [ for ({ for ($withDuplicates) .tid : . }) .value ]`.
3. Use `$unique` (or guard with `if ($unique) $unique else []` if you need an empty array when there’s nothing).

**Try it:** [JSLT demo playground](https://www.garshol.priv.no/jslt-demo).
