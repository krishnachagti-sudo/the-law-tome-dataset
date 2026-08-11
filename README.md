# The Law Tome — dataset

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21889681.svg)](https://doi.org/10.5281/zenodo.21889681)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-blue.svg)](https://creativecommons.org/licenses/by/4.0/)

A machine-readable index of **1,116 named laws, principles, effects, razors, paradoxes
and fallacies**, each traced to a primary source and rated for how much evidence actually
sits behind it.

Everyone quotes these. Almost nobody checks them. Parkinson's Law was a satirical essay
in *The Economist* in 1955. Hofstadter's Law is a self-referential joke. The planning
fallacy quoted beside it is Kahneman and Tversky, empirical, with a documented remedy.
This dataset makes that difference a queryable field.

Full explanations, examples and limits live on the site: **https://conyso.com/lawtome/**

## Files

| File | Description |
|---|---|
| `data/lawtome.json` | Complete index with sources and graph edges (1.6 MB) |
| `data/lawtome.csv` | Flat summary, one row per law (152 KB) |

## Schema

Each record in `laws[]`:

| Field | Type | Notes |
|---|---|---|
| `no` | string | Zero-padded index number |
| `slug` | string | URL-safe identifier, stable |
| `name` | string | Canonical name |
| `aliases` | string[] | Other names the idea travels under |
| `category` | string | One of 20 fields |
| `reliability` | string | `Empirical` · `Heuristic` · `Contested` · `Folk-adage` |
| `statement` | string | The law in the form it actually gets quoted |
| `coinedYear` | number | Year first stated (present for 836 of 1,116) |
| `namedAfter` | string | The person the name honours |
| `sameAs` | string | Wikipedia URL where one exists |
| `sources` | object[] | `{text, url, type}` — 2,489 source records in total |
| `related` | object[] | `{slug, kind}` — 2,167 graph edges |
| `url` | string | Canonical page on the site |

## What's in it

**Reliability** — the field that makes this different from a list:

| Rating | Count | Share |
|---|---|---|
| Empirical | 609 | 54.6% |
| Contested | 257 | 23.0% |
| Heuristic | 225 | 20.2% |
| Folk-adage | 25 | 2.2% |

Just under half the index rests on something other than measurement.

**Coverage** — 20 fields, led by philosophy (150), psychology (144), physics (129) and
economics (111). Coined years span **1609 to 2014**.

**Graph** — `related` edges are typed (`near-twin`, `consequence`, `in-tension` and
others), so laws that contradict each other are queryable, not just adjacent.

## Quick start

```python
import json, collections

laws = json.load(open("data/lawtome.json"))["laws"]

# Everything people quote as fact that isn't a finding
soft = [l for l in laws if l["reliability"] in ("Folk-adage", "Heuristic")]
print(len(soft), "of", len(laws))

# Laws in tension with another law
tense = [l for l in laws
         if any(r["kind"] == "in-tension" for r in l.get("related", []))]
```

```bash
# Everything empirical in economics, oldest first
python3 -c "
import json
laws = json.load(open('data/lawtome.json'))['laws']
rows = [l for l in laws if l['category']=='economics' and l['reliability']=='Empirical']
for l in sorted(rows, key=lambda x: x.get('coinedYear') or 9999):
    print(l.get('coinedYear'), l['name'])
"
```

## License

**CC BY 4.0.** Use it, redistribute it, build on it, including commercially. Attribution:

> The Law Tome — https://conyso.com/lawtome/

## Citation

```bibtex
@dataset{lawtome,
  title     = {The Law Tome: an index of 1,116 named laws, principles and effects},
  author    = {Chagti, Krishna},
  year      = {2026},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.21889681},
  url       = {https://doi.org/10.5281/zenodo.21889681},
  note      = {CC BY 4.0}
}
```

## Corrections

If an attribution, date or source is wrong, open an issue. Getting these right is the
entire point of the project, so corrections are genuinely welcome rather than merely
tolerated.
