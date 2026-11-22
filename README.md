# fuzzy

Fuzzy matching for [Quirk](https://github.com/AlexVachon/quirk), the kind an
editor's quick open (Ctrl+P) and command palette use: the query's letters, in
order, anywhere in the text, ignoring case. Matches are scored so the ones you
mean come first, and give the matched positions so they can be drawn bold.

```quirk
use fuzzy

m := fuzzy.find("gfn", "getFileName")      // null when it doesn't match
print(m.score, m.positions)                 // positions: [0, 3, 7]

// Best first; ties go to the shorter text, then the earlier item.
for x in fuzzy.filter("ide", files, paths=true, limit=50) {
    hit: fuzzy.Match = x
    print(hit.text, hit.score)              // also hit.item, hit.index
}

// Commands, by a key:
fuzzy.filter("tt", commands, key=fn(c) { return c.title })

// [[piece, matched], ...] for drawing:
fuzzy.runs("src/ide.qk", [4, 5, 6])         // [["src/", false], ["ide", true], [".qk", false]]
```

What scores more:

- letters at word starts (after `/ \ _ - . :` or a space, a camelCase hump)
  and the text's first letter;
- letters in a run (`term` in `terminal` beats `t-e-r-m`);
- fewer and shorter gaps, an early first letter;
- with `find_path` / `filter(paths=true)`: a query found in the file's name
  alone (`ide` → `src/ide.qk` before `docs/guide.md`). A query with a `/`
  matches over the whole path.

Spaces in the query are ignored, so `open file` finds `File: Open File`.

## API

| | |
| --- | --- |
| `find(query, text) -> Match` | null if no match; `score`, `positions` |
| `find_path(query, path) -> Match` | the file name counts more |
| `filter(query, items, key=null, paths=false, limit=0) -> List[Match]` | best first; also `item`, `index`, `text`; an empty query keeps all, in order |
| `runs(text, positions) -> List` | `[piece, matched]` pairs |

## Tests

```
quirk test
```
