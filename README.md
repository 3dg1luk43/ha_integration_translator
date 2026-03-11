# HA Integration Translator

Standalone translation utility for Home Assistant custom integrations.

This repository contains `translate.py`, a script that translates integration locale files from canonical `en.json` into Home Assistant-supported languages using `deep-translator`.

## Features

- Translates missing keys from `en.json` into target languages
- Detects and updates keys changed in English using git baseline data
- Removes keys deleted from English source files
- Preserves placeholder arguments such as `{entity}` and `{value}`
- Updates frontend card translation objects in JavaScript (`const TRANSLATIONS = { ... }`)
- Supports `--all` mode from Home Assistant language metadata

## Requirements

- Python 3.10+
- `requests`
- `deep-translator`

Install dependencies:

```bash
pip install -r requirements.txt
```

## Usage

```bash
python3 translate.py <translations_dir> [languages...] [options]
```

Options:

- `--all` Generate for all Home Assistant supported languages
- `--retranslate` Retranslate all existing keys
- `--card-file <path>` Update card translations in JS file

Examples:

```bash
python3 translate.py ./custom_components/ha_washdata/translations de fr
python3 translate.py ./custom_components/ha_washdata/translations --all --card-file ./custom_components/ha_washdata/www/ha-washdata-card.js
```

## Development

```bash
python3 -m py_compile translate.py
```

## License

See [LICENSE](LICENSE).
