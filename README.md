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

## Card Translation Support

The script can also translate frontend card labels stored in a JavaScript object:

- It looks for `const TRANSLATIONS = { ... }` in the file passed to `--card-file`
- `en` is the canonical source for card keys and values
- It updates only requested target languages (or all in `--all` mode)
- It removes keys in target card languages that no longer exist in `en`
- Placeholder tokens such as `{name}` and `{value}` are preserved

### Expected Card Format

`TRANSLATIONS` must be JSON-compatible inside the object literal:

- Top-level object keyed by language code (`en`, `de`, `fr`, ...)
- Each language value is a flat key/value object
- Use double quotes for keys and string values
- Trailing commas are tolerated

Minimal example:

```js
const TRANSLATIONS = {
	"en": {
		"status": "Status",
		"remaining": "Remaining: {minutes} min"
	},
	"de": {
		"status": "Status"
	}
};
```

In this example, running the script fills missing `de.remaining` based on `en.remaining` and preserves `{minutes}`.

Examples:

```bash
python3 translate.py ./custom_components/ha_washdata/translations de fr
python3 translate.py ./custom_components/ha_washdata/translations --all --card-file ./custom_components/ha_washdata/www/ha-washdata-card.js
```

Card-only style run (for one language target set):

```bash
python3 translate.py ./custom_components/ha_washdata/translations de --card-file ./custom_components/ha_washdata/www/ha-washdata-card.js
```

## GitHub Action Template

This repository provides a reusable composite action at:

- `3dg1luk43/ha_integration_translator/.github/actions/translate@main`

You can call it from any repository workflow after checkout.

Minimal usage:

```yaml
name: Translate Integration Strings

on:
	workflow_dispatch:

jobs:
	translate:
		runs-on: ubuntu-latest
		steps:
			- uses: actions/checkout@v4

			- name: Run translator action
				uses: 3dg1luk43/ha_integration_translator/.github/actions/translate@main
				with:
					translations_dir: custom_components/ha_washdata/translations
					all_languages: "true"
					card_file: custom_components/ha_washdata/www/ha-washdata-card.js
```

Action inputs:

- `translations_dir` (required): directory containing `en.json`
- `languages`: space-separated list, for example `"de fr es"`
- `all_languages`: `"true"` or `"false"`
- `retranslate`: `"true"` or `"false"`
- `card_file`: optional JS file containing `const TRANSLATIONS = { ... }`
- `python_version`: optional, default `3.11`

Full template with commit/push example:

- `examples/translate-workflow-template.yml`

## Development

```bash
python3 -m py_compile translate.py
```

## License

See [LICENSE](LICENSE).
