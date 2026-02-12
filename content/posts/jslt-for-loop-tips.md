---
title: "JSLT tips: the for-loop and filtering arrays"
date: "2025-02-12"
tags:
  - jslt
  - json
  - tips
---

[JSLT](https://github.com/schibsted/jslt) is a JSON transformation language. One pattern I use a lot: build an array with `let`, then use a **for-loop with an `if`** to only include items that pass a condition. No separate filter step—you output and filter in one go.

---

### Simple example

**Input:**

```json
{
  "items": [
    { "name": "a", "count": 2 },
    { "name": "b", "count": 0 },
    { "name": "c", "count": 1 }
  ]
}
```

**JSLT:** only include objects where `count` is truthy:

```json
{
  "active": [ for (.items) . if (.count) ]
}
```

**Output:**

```json
{
  "active": [
    { "name": "a", "count": 2 },
    { "name": "c", "count": 1 }
  ]
}
```

Pattern: `[ for (<array>) . if (<condition>) ]` — loop over the array, output the current element (`.`) only when the condition holds. Result is a new array with no nulls or extra logic.

---

### Generalizing: build then filter in the output

When you have a list of name/value pairs (or similar) and only want to keep entries with a value, build the array in a `let`, then filter in the for-loop.

**Input:**

```json
{
  "id": "123",
  "label": "Widget Co",
  "code": "W-001",
  "description": "",
  "serviceRef": "svc-1",
  "resourceKey": "res-1",
  "tagValue": "100"
}
```

**Template:**

```json
let attributes = [
  { "name": "ID", "value": .id },
  { "name": "LABEL", "value": .label },
  { "name": "CODE", "value": .code },
  { "name": "DESCRIPTION", "value": .description }
]
let tags = [
  { "name": .tagValue, "type": "TAG", "resourceType": "Tag" }
]

{
  "resourceType": "Resource",
  "name": .resourceKey,
  "attributes": [ for ($attributes) . if (.value) ],
  "tags": [ for ($tags) . if (.name) ]
}
```

- **`attributes`** and **`tags`** are built once with `let`.
- **`[ for ($attributes) . if (.value) ]`** keeps only objects where `value` is non-null/non-empty.
- **`[ for ($tags) . if (.name) ]`** does the same for `name`.

You define the shape of each entry in one place and drop empty ones in the for-loop, without a separate filter step.

---

### More for-loop patterns

**1. Filter by multiple conditions**

Same `[ for (...) . if (...) ]` shape; the condition can combine fields:

```json
[ for ($items) . if (.type == "foo" and .kind == "bar") ]
```

Use `and` / `or` as needed. Result: only elements that match all (or any) of the criteria.

---

**2. Filter over a nested array**

Run the for-loop over a path that’s already narrowed down (e.g. first match, then its children):

```json
let matches = [ for ($topologies) . if (.type == "foo") ]
let links = $matches[0].links
[ for ($links) . if (.source == "bar") ]
```

First line gets the list of matching parents; then you take one (e.g. `[0]`) and loop over `.links` (or any nested array) and filter that. Handy when you “get the first X, then work with its Y.”

---

**3. Nested for: “items in A that match something in B”**

Bind a value from the outer element and use it in the inner loop (e.g. find all items in one list whose `id` appears in another):

```json
flatten([
  for ($listA)
  let id = .id
  [ for ($listB) . if ($id == .id) ]
])
```

Outer loop: for each element in `$listA`, set `id = .id`. Inner loop: over `$listB`, keep only elements where `.id` equals that `id`. You get an array of arrays (one per outer item), so `flatten(...)` turns it into a single list of matches. Same idea works for “shared” or “intersection” logic.

---

**4. Dedupe by key**

If the previous step (or any step) leaves duplicates by a key (e.g. `tid`), use an object as a key-value map and then take values:

```json
let withDuplicates = [ ... ]
[ for ({ for ($withDuplicates) .tid : . }) .value ]
```

Duplicate `.tid` overwrites in the object, so you end up with one entry per key. See [JSLT: removing duplicates by key](/pages/posts/jslt-removing-duplicates/) for more.

---

**5. Guard the result**

When the result might be null or you must return an array:

```json
if ($result) $result else []
```

Use this at the end of a function so callers always get an array when there are no matches.

---

**Try it:** [JSLT demo playground](https://www.garshol.priv.no/jslt-demo) — paste your input and template and run.
