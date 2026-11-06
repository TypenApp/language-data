# Typen Global Language Library (`TypenApp/language-data`)

This repository contains the lexicon ingestion pipeline, binary compiler, homophone confusion sets, 6 specialized domain vocabulary packs, and held-out quality test suites that power [**Typen**](https://github.com/TypenApp/typen).

- **Main Application**: [`TypenApp/typen`](https://github.com/TypenApp/typen)
- **Model Resources**: [`TypenApp/model-resources`](https://github.com/TypenApp/model-resources)
- **Documentation**: [`TypenApp/docs`](https://github.com/TypenApp/docs)

---

## What Lives Here

| Path | Description |
| --- | --- |
| `pipeline/ingest.py` | Master English dataset builder combining SCOWL/ESDB headwords, dialect tags (`EN_GB`, `EN_US`, `EN_CA`, `EN_AU`), wordfreq tiers, and direct typo maps |
| `pipeline/external_sources.py` | Loader for `pipeline/data/external/scowl-pre.txt` (166,475 attested English words and inflections) |
| `pipeline/validate.py` | Dataset sanitisation and invariant checks (ensures known misspellings never enter the valid lexicon) |
| `pipeline/compile_binary.py` | Zero-copy `mmap` binary compiler (`lexicon.bin`, `lexicon_meta.bin`, `symspell_idx.bin`, `direct_typos.bin`, `context_ngram.bin`, `abbreviations.bin`) |
| `pipeline/regression_runner.py` | Golden corpus regression runner enforcing minimum typo accuracy, confusion accuracy, and near-zero false-positive rate |
| `pipeline/sign_release.py` | Ed25519 manifest signer and Zstandard `.tar.zst` full / `.patch.zst` delta archive builder |
| `resources/en_base/` | Compiled binary baseline bundle (`schema_version = 1`) and `manifest.json` |
| `resources/en_base/packs/` | 6 domain vocabulary packs: `programming`, `medicine`, `finance`, `law`, `business`, `academic` |
| `tests/` | Held-out corpus quality gate (`test_engine_quality_heldout.py`); the full curated suite lives in `TypenApp/typen` |

---

## Building & Verifying the Language Bundle

```bash
python -m venv .venv && . .venv/bin/activate
pip install -e ".[dev]"

# Compile and run the golden corpus regression gate
python -m pipeline.cli resources/en_base

# Run the held-out quality test suite
pytest
```

---

## Reporting Missed Typos or False Positives

Open a [**Correction Report**](https://github.com/TypenApp/language-data/issues/new?template=correction_report.yml) if you find:
1. A misspelling that Typen did not fix.
2. A valid word, proper noun, or technical term that Typen wrongly corrected.
3. Terms to add to one of the 6 domain vocabulary packs (`resources/en_base/packs/*.pack.json`).

## Licence

MIT. See [`LICENSE`](LICENSE) and [`pipeline/data/external/README.md`](pipeline/data/external/README.md) for SCOWL/ESDB word list notices.
