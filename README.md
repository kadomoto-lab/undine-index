# undine-index

The default package index of [Undine](https://github.com/kadomoto-lab/undine-eda), an AI-native, web-based EDA
environment for custom IC design. `python -m undine pkg add PROJECT NAME` looks a package up here, unless the project
names another index.

This repository holds no design files. It lists, for each published version of a package, the git repository and
commit that hold it and the hash of its files. Undine fetches that commit and refuses it unless its files hash to
the listed tree.

## Layout

```
config.json                 {"name": "undine", "format": 1}
an/al/analog_ip.jsonl       one JSON line per version, appended
3/i/inv.jsonl               names of three letters: 3/<first letter>/
2/op.jsonl                  names of one or two letters: 1/ or 2/
```

The paths follow crates.io-index: a name of four letters or more goes under its first two and next two letters.

A line:

```json
{"name": "analog_ip", "vers": "1.2.0", "git": "https://github.com/me/analog_ip.git", "subdir": "lib",
 "commit": "<full commit id>", "tree": "sha256:<64 hex>",
 "pdk": {"family": "sky130", "variants": ["sky130A"]},
 "deps": [{"name": "sar_common", "req": "^1.2"}],
 "exports": ["bandgap", "ldo"], "license": "Apache-2.0", "yanked": false}
```

A version's commit and tree never change. Yanking a version sets `"yanked": true`: resolution skips it, and a
project that already locks it keeps it with a warning.

The format is described in full in
[docs/design/packages.md](https://github.com/kadomoto-lab/undine-eda/blob/main/docs/design/packages.md#index).

## Publishing

Publishing a version is a pull request that adds its line, and yanking one is a pull request that sets `yanked`.
The PR should link the publisher's check report (DRC, LVS and spec results for the commit). kadomoto reviews the PRs
for now.

The `pkg init / check / pack / publish` commands that write the line and open the PR are not built yet. Until they
are, a line is written by hand.

## License

The index (`config.json`, the package files and their lines) is under the [Apache License 2.0](LICENSE). A line's
`license` field names the license of that package's own files, which stay in the package's repository.
